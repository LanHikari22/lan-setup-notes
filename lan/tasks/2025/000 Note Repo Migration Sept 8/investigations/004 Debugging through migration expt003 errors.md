---
parent: '[[000 Note Repo Migration Sept 8]]'
spawned_by: '[[001 Parse through all notes and classify as core - peripheral - partial]]'
context_type: investigation
status: done
---

\#debugging

Parent: [000 Note Repo Migration Sept 8](../000%20Note%20Repo%20Migration%20Sept%208.md)

Spawned by: [001 Parse through all notes and classify as core - peripheral - partial](../tasks/001%20Parse%20through%20all%20notes%20and%20classify%20as%20core%20-%20peripheral%20-%20partial.md)

Spawned in: [<a name="spawn-invst-0f35dc" />^spawn-invst-0f35dc](../tasks/001%20Parse%20through%20all%20notes%20and%20classify%20as%20core%20-%20peripheral%20-%20partial.md#spawn-invst-0f35dc)

# 1 Journal

2025-09-13 Wk 37 Sat - 23:40 +03:00

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt003_working_items_of_vault ~/src/cloned/gh/LanHikari22/lan-setup-notes/lan

# out (error, expected)
Folder passed is not a valid obsidian vault
````

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt003_working_items_of_vault ~/src/cloned/gh/LanHikari22/lan-setup-notes

# out (error, unexpected)
Failed to get working items
````

Okay, time to get some more detailed logging!

2025-09-14 Wk 37 Sun - 00:01 +03:00

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo add log
````

Checked [docs.rs log](https://docs.rs/log/latest/log/)

2025-09-14 Wk 37 Sun - 00:34 +03:00

Hmm, there are a lot of `?` operators for possible failures. We can find a failure with `:%s/?/.unwrap()/g`

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt003_working_items_of_vault ~/src/cloned/gh/LanHikari22/lan-setup-notes

# out (error, relevant)
thread 'main' panicked at src/cluster_note.rs:348:87:
called `Option::unwrap()` on a `None` value
````

We're failing at parsing a `NormalNoteFilePath` when we expect to just encounter a non-cluster related note.

We can revert the unwraps with `:%s/.unwrap\(\)/?/g`. This works for me because I didn't use unwraps in the file.

2025-09-14 Wk 37 Sun - 00:46 +03:00

We need to use

````sh
log::set_max_level(log::LevelFilter::Trace);
````

But we also need to configure this for stdout. We can also use [fstdout-logger](https://docs.rs/fstdout-logger/latest/fstdout_logger/) for defaults, file logs, and apparently even colored outputs.

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo add fstdout-logger
````

````rust
    let config = LoggerConfig::builder()
        .level(LevelFilter::Trace)
        .use_colors(true)
        .build();

    init_logger_with_config(Some("debug.log"), config).expect("Failed to initialize logger");

    log::info!("Hiiiii");

````

![Pasted image 20250914004956.png](../../../../../attachments/Pasted%20image%2020250914004956.png)

Cool!

2025-09-14 Wk 37 Sun - 00:52 +03:00

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt003_working_items_of_vault ~/src/cloned/gh/LanHikari22/lan-setup-notes

# out (error, relevant)
[00:52:00 DEBUG src/cluster_note.rs:157] path: "/home/lan/src/cloned/gh/LanHikari22/lan-setup-notes/.gitignore"
thread 'main' panicked at src/bin/expt003_working_items_of_vault.rs:27:10:
````

So we are failing on non-markdown files instead of just skipping them. Let's fix that.

2025-09-14 Wk 37 Sun - 00:58 +03:00

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt003_working_items_of_vault ~/src/cloned/gh/LanHikari22/lan-setup-notes

# out (error, relevant)
[00:58:03 ERROR src/cluster_note.rs:207] not a core file path!: "/home/lan/src/cloned/gh/LanHikari22/lan-setup-notes/lan/tasks/2025/000 Note Repo Migration Sept 8/000 Note Repo Migration Sept 8.md"
````

It should be.

````rust
pub fn is_cluster_core_file_path(path: &PathBuf) -> Option<bool> {
    if !path.is_file() || !path.exists() || !path.ends_with(".md") {
        log::error!("Not a markdown file: {path:?}");
        log::error!("is_file: {}", path.is_file());
        log::error!("exits: {}", path.exists());
        log::error!("ends_with_md: {}", path.ends_with(".md"));
        return Some(false);
    }
````

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt003_working_items_of_vault ~/src/cloned/gh/LanHikari22/lan-setup-notes

# out (error, relevant)
[01:02:20 ERROR src/cluster_note.rs:130] Not a markdown file: "/home/lan/src/cloned/gh/LanHikari22/lan-setup-notes/lan/tasks/2025/000 Note Repo Migration Sept 8/000 Note Repo Migration Sept 8.md"
[01:02:20 ERROR src/cluster_note.rs:131] is_file: true
[01:02:20 ERROR src/cluster_note.rs:132] exits: true
[01:02:20 ERROR src/cluster_note.rs:133] ends_with_md: false
````

I guess I missed this with the [`ends_with`](https://doc.rust-lang.org/std/path/struct.Path.html#examples) docs:

````
assert!(!path.ends_with("conf")); // use .extension() instead
````

2025-09-14 Wk 37 Sun - 01:17 +03:00

````rust
pub fn is_normal_markdown_file_path(path: &PathBuf) -> Option<bool> {
    log::trace!("path: {path:?}");
    if !path.is_file() || !path.exists() || path.extension().unwrap() != "md" {
        return Some(false);
    }
````

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt003_working_items_of_vault ~/src/cloned/gh/LanHikari22/lan-setup-notes

# out (error, relevant)
[01:16:53 TRACE src/cluster_note.rs:157] path: "/home/lan/src/cloned/gh/LanHikari22/lan-setup-notes/.gitignore"
````

`.gitignore` is still gonna fail the `.extension()` check.

We can't make this assumption:

````sh
if !path.is_file() || !path.exists() || path.extension()? != "md" {
	return Some(false);
}
````

If there's no extension, we just output false.

2025-09-14 Wk 37 Sun - 01:32 +03:00

````
pub fn is_cluster_category_folder(folder: &PathBuf) -> Option<bool> {
    log::trace!("0 folder: {folder:?}");
    let folder_name = folder.file_name().unwrap().to_str().unwrap();

    log::trace!("1 folder: {folder:?}");

    if !CONTEXT_TYPE_FOLDERS.contains(&folder_name) {
        return Some(false);
    }

    log::trace!("2 folder: {folder:?}");

    if !is_cluster_root_folder(&folder.parent().unwrap().to_path_buf()).unwrap() {
        return Some(false);
    }
    log::trace!("OK folder: {folder:?}");

    Some(true)
}
````

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt003_working_items_of_vault ~/src/cloned/gh/LanHikari22/lan-setup-notes

# out (error, relevant)
[01:32:01 TRACE src/cluster_note.rs:129] OK folder: "/home/lan/src/cloned/gh/LanHikari22/lan-setup-notes/lan/topics/tooling/windows/tasks/2025/000 Fix Windows Laptop not booting/inferences"
[01:32:01 TRACE src/cluster_note.rs:118] 1 folder: "/home/lan/src/cloned/gh/LanHikari22/lan-setup-notes/lan/topics/tooling/windows/tasks/2025/000 Fix Windows Laptop not booting/side-notes"
````

Okay, this is an outdated cluster note category we forgot to change. Let's change it in the vault.

2025-09-14 Wk 37 Sun - 01:40 +03:00

OK! Now we get all working items!
