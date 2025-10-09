---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[009 Impl and carry out cluster note migration]]"
context_type: task
status: todo
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[009 Impl and carry out cluster note migration]]

Spawned in: [[009 Impl and carry out cluster note migration#^spawn-task-479e16|^spawn-task-479e16]]

# 1 Journal

2025-09-30 Wk 40 Tue - 08:52 +03:00

The worry now is the fact that old content is necessary to have for cmark $\to$ obsidian markdown patching, we likely will have to split the file at the text level and not just the event level. It is however possible that the patching rejects anything but the relevant window, so we can try that.

2025-09-30 Wk 40 Tue - 09:11 +03:00

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin app -- extract_old_format_records /home/lan/src/cloned/gh/deltatraced/delta-trace/

# out (error, relevant)
[2025-09-30T06:18:29Z INFO  app] processing "/home/lan/src/cloned/gh/deltatraced/delta-trace/lan/topics/practice/ctf/topic/entries/2025/000 Solving Mountain and Dragon CTF.md"

thread 'main' panicked at src/bin/app.rs:98:64:
Could not process links: LinkExtractError(MustHaveZeroOrOneBar(3, "    command = (\n        commands\n        | pipe.OfIter[CommandData].filter(\n            lambda command: command.activation_byte == command_byte\n        )\n        | pipe.OfIter[CommandData].to_list()\n        | pipe.Of[List[CommandData"))
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```


2025-09-30 Wk 40 Tue - 09:23 +03:00

This is in a code block, there cannot exist links there. And we already make sure to only process `Text` events, so what gives?

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt000_parse_single_pulldown_cmark "/home/lan/src/cloned/gh/deltatraced/delta-trace/lan/topics/practice/ctf/topic/entries/2025/000 Solving Mountain and Dragon CTF.md" | less

# out (relevant)
Event Start(CodeBlock(Fenced(Borrowed("py"))))
Event Text(Borrowed("    command = (\n        commands\n        | pipe.OfIter[CommandData].filter(\n            lambda command: command.activation_byte == command_byte\n        )\n        | pipe.OfIter[CommandData].to_list()\n        | pipe.Of[List[CommandData]].map(lambda lst: lst[0] if len(lst) == 1 else None)\n    )\n\n    if command is None:\n        return (None, tape)\n"))
Event End(CodeBlock)
```

We need to track that a code block tag is not currently open.

2025-09-30 Wk 40 Tue - 09:34 +03:00

This is both for `fn extract_obsidian_md_links` and `fn extract_linkable_obsidian_md_items`

2025-09-30 Wk 40 Tue - 09:44 +03:00

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin app -- extract_old_format_records /home/lan/src/cloned/gh/deltatraced/delta-trace/

# out (relevant)
[2025-09-30T06:39:14Z INFO  app] processing "/home/lan/src/cloned/gh/deltatraced/delta-trace/lan/topics/practice/tutorials/ai/tensorflow.org/entries/2025/000 Transformer Tensorflow Tutorial.md"
[2025-09-30T06:39:14Z INFO  app] processing "/home/lan/src/cloned/gh/deltatraced/delta-trace/lan/topics/practice/ctf/topic/entries/2025/000 Solving Mountain and Dragon CTF.md"
[2025-09-30T06:43:05Z INFO  app] processing "/home/lan/src/cloned/gh/deltatraced/delta-trace/lan/topics/practice/ctf/2025/google-ctf-2025/during/entries/000 overall.md"

[2025-09-30T06:43:05Z INFO  app] processing "/home/lan/src/cloned/gh/deltatraced/delta-trace/lan/topics/practice/ctf/2025/google-ctf-2025/during/topics/reversing/entries/000 Multiarch.md"
[2025-09-30T06:43:05Z ERROR migration_rs::cluster_note] Could not get any content within H2: [Start(Heading { level: H2, id: None, classes: [], attrs: [] }), Text(Borrowed("3.1 Setup ")), Start(Link { link_type: Inline, dest_url: Borrowed("https://github.com/VirusTotal/yara"), title: Borrowed(""), id: Borrowed("") }), Text(Borrowed("yara")), End(Link)]
```

So two things. One, I thought it wasn't terminating because it took 4 minutes to process that CTF file for some reason. And two, the new error.

2025-09-30 Wk 40 Tue - 10:09 +03:00

```
## 3.1 Setup [yara](https://github.com/VirusTotal/yara)
```

We should not have links in headings. Change that manually.

2025-09-30 Wk 40 Tue - 10:14 +03:00

Let's run it in release to be faster.

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --release --bin app -- extract_old_format_records /home/lan/src/cloned/gh/deltatraced/delta-trace/


# out (relevant)
[2025-09-30T07:14:48Z INFO  app] processing "/home/lan/src/cloned/gh/deltatraced/delta-trace/lan/topics/practice/ctf/topic/entries/2025/000 Solving Mountain and Dragon CTF.md"
[2025-09-30T07:15:10Z INFO  app] processing "/home/lan/src/cloned/gh/deltatraced/delta-trace/lan/topics/practice/ctf/2025/google-ctf-2025/during/entries/000 overall.md"

[2025-09-30T07:15:10Z INFO  app] processing "/home/lan/src/cloned/gh/deltatraced/delta-trace/lan/topics/practice/ctf/2025/google-ctf-2025/during/topics/crypto/entries/003 NUMEROLOGY.md"
[2025-09-30T07:15:10Z ERROR migration_rs::cluster_note] Could not get any content within H2: [Start(Heading { level: H2, id: None, classes: [], attrs: [] }), Text(Borrowed("4.1 Setup ")), Start(Link { link_type: Inline, dest_url: Borrowed("https://github.com/RustCrypto/stream-ciphers/tree/master/chacha20"), title: Borrowed(""), id: Borrowed("") }), Text(Borrowed("gh RustCrypto chacha20")), End(Link)]
```

It's faster, though still takes noticably more than the others.

Fixing more links in headings. We're also running writeback and then commiting so that writeback would yield identity and not interfere with diffs.

2025-10-06 Wk 41 Mon - 04:53 +03:00

Spawn [[003 LinkExtractError due to token including a comma when it should end in brackets]] ^spawn-issue-b66790

2025-10-06 Wk 41 Mon - 10:00 +03:00

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
git commit -m "fix obsidian link tokenization breaking on utf-8"

# out
Trim Trailing Whitespace.................................................Passed
Check Yaml...........................................(no files to check)Skipped
Check for added large files..............................................Passed
Check formatting.........................................................Passed
Run tests................................................................Passed
Check clippy lints.......................................................Passed
[main c355d22] fix obsidian link tokenization breaking on utf-8
 2 files changed, 335 insertions(+), 40 deletions(-)
```

2025-10-06 Wk 41 Mon - 09:56 +03:00

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --release --bin app -- extract_old_format_records /home/lan/src/cloned/gh/deltatraced/delta-trace/
```

2025-10-06 Wk 41 Mon - 10:39 +03:00

after turning some `Option<T>` into explicit function errors:

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --release --bin app -- extract_old_format_records /home/lan/src/cloned/gh/deltatraced/delta-trace/

# out (error, relevant)
[2025-10-06T07:38:32Z INFO  app] value_path: ObsidianVaultPath { path: "/home/lan/src/cloned/gh/deltatraced/delta-trace/" }

thread 'main' panicked at /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration/src/drivers.rs:44:10:
Failed to get working items: GetCategoryFoldersError(NotAClusterCategoryFolderPath("/home/lan/src/cloned/gh/deltatraced/delta-trace/lan/protos/2025/001 Rust Diesel Event Sourcing/tasks/2025/000 Implement the Event Accumulator/judgments"))
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```

Yup, we recently added new categories `judgment` and `concept`, but they're not reflected here. Let's add them.

2025-10-06 Wk 41 Mon - 10:47 +03:00

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --release --bin app -- extract_old_format_records /home/lan/src/cloned/gh/deltatraced/delta-trace/

# out (relevant)
[OK]
```

```sh
# in /home/lan/src/cloned/gh/deltatraced/delta-trace
git status | wc -l

# out
47
```

2025-10-06 Wk 41 Mon - 10:57 +03:00

Let's apply writeback again first on a clean state,

```sh
# in /home/lan/src/cloned/gh/deltatraced/delta-trace
rm -rf * && git reset --hard

# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin app -- writeback /home/lan/src/cloned/gh/deltatraced/delta-trace/
```

```sh
# in /home/lan/src/cloned/gh/deltatraced/delta-trace
git commit -m "filtering vault for commonmark writeback compliance"

# out
[main 634f74a] filtering vault for commonmark writeback compliance
 28 files changed, 54 insertions(+), 80 deletions(-)
```

2025-10-06 Wk 41 Mon - 10:53 +03:00

There are problems. Let's start with `/home/lan/src/cloned/gh/deltatraced/delta-trace/lan/topics/read/papers/ai/entries/2025/000 Attention is all you need.md`. 

2025-10-06 Wk 41 Mon - 11:11 +03:00

```rust
// in fn app_extract_old_format_records
// TODO: Temporary for debugging
if let Some(filename) = path.file_name() {
	if let Some(s) = filename.to_str() {
		if !s.contains("Attention is all you need") {
			return Some(());
		}
	}
}
```

2025-10-06 Wk 41 Mon - 11:18 +03:00

The output has nonesensical content like

```
## 3.1 On Self Attention

##\\\ \\\\_\\3.2 O_\\\\\\_\_\\\_\\_\__\\\\\\\\_\\__\\\\\\__\___n S\\\_\o_\\\ftmax\\\

\\\\\\\\_\_\__\\\\__\__\\\\__\__\\\\\# 4\__ _\\_\_\\\__\\____\__References
```

And the suspect is likely because we tried to do adhoc diff patching on a segment comparing against the whole:

```rust
// in fn app_extract_old_format_records
let content = common::read_file_content(path).expect("Could not read content");

// ...

let new_content_without_old_format_records =
	common::render_events_to_common_markdown(&events_excluding_old_format_records)
		.expect("Failed to render back to common markdown")
		.pipe(|new_data| {
			common::adhoc_fix_rendered_markdown_output_for_obsidian(&content, &new_data)
		});
```

2025-10-06 Wk 41 Mon - 11:47 +03:00

Spawn [[000 Choosing pulldown_cmark for cluster note migration was costly]] ^spawn-infer-290240

Spawn [[000 To perform the file-level old record transformations for migration using low level string splitting]] ^spawn-jdgmt-4ce159

2025-10-06 Wk 41 Mon - 12:48 +03:00

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
git commit -m "updated context types and added more error handling"

# out
Trim Trailing Whitespace.................................................Passed
Check Yaml...........................................(no files to check)Skipped
Check for added large files..............................................Passed
Check formatting.........................................................Passed
Run tests................................................................Passed
Check clippy lints.......................................................Passed
[main 7ad8a84] updated context types and added more error handling
 3 files changed, 129 insertions(+), 37 deletions(-)
```

2025-10-06 Wk 41 Mon - 12:56 +03:00

Spawn [[014 impl get_note_old_format_entries_from_content]] ^spawn-task-0a71ba

2025-10-07 Wk 41 Tue - 05:20 +03:00

Implementing creating a peripheral file for each entry extracted

2025-10-07 Wk 41 Tue - 05:40 +03:00

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --release --bin app -- extract_old_format_records /home/lan/src/cloned/gh/deltatraced/delta-trace/

# out (error, relevant)
[2025-10-07T02:39:28Z INFO  app] processing "/home/lan/src/cloned/gh/deltatraced/delta-trace/lan/topics/read/papers/ai/entries/2025/000 Attention is all you need.md"

thread 'main' panicked at src/bin/app.rs:145:18:
Failed to create new peripheral note: AssertError(NotAPeripheralNotePath("/home/lan/src/cloned/gh/deltatraced/delta-trace/lan/topics/read/papers/ai/entries/2025/000 Attention is all you need/tasks/000 Find a working example implementation of Attention in section 3.2.md"))
```

Oh oops, it can't exist as a valid peripheral note path when it wasn't created yet!

2025-10-07 Wk 41 Tue - 05:57 +03:00

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --release --bin app -- extract_old_format_records /home/lan/src/cloned/gh/deltatraced/delta-trace/

# out (error, relevant)
[2025-10-07T02:46:32Z INFO  app] processing "/home/lan/src/cloned/gh/deltatraced/delta-trace/README.md"

thread 'main' panicked at src/common.rs:866:22:
byte index 19 is not a char boundary; it is inside '❤' (bytes 18..21) of `  <sub>Built with ❤︎ by Mohammed Alzakariya</sub>`
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```

This is due to

```rust
pub fn count_occurances_in_str(s: &str, patt: &str) -> usize {
    let mut mut_i = 0;
    let mut mut_count = 0;

    while mut_i < s.len() {
        if mut_i + patt.len() > s.len() {
            break;
        }

        let segt = &s[mut_i..mut_i + patt.len()];

        if segt == patt {
            mut_count += 1;
            mut_i += patt.len();
        } else {
            mut_i += 1;
        }
    }

    mut_count
}
```

We can't make buffer assumptions with strings. They're generally not safe, unless we convert them to bytes first. 

Spawn [[005 Count substring occurances in string in rust]] ^spawn-howto-a57747

2025-10-07 Wk 41 Tue - 06:18 +03:00

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --release --bin app -- extract_old_format_records /home/lan/src/cloned/gh/deltatraced/delta-trace/

# out (error, relevant)
[2025-10-07T03:17:49Z INFO  app] processing "/home/lan/src/cloned/gh/deltatraced/delta-trace/lan/topics/practice/ctf/topic/entries/2025/000 Solving Mountain and Dragon CTF.md"

thread 'main' panicked at src/bin/app.rs:145:18:
Failed to create new peripheral note: Io(Os { code: 2, kind: NotFound, message: "No such file or directory" })
```

2025-10-07 Wk 41 Tue - 06:23 +03:00

Remember to rerun the migration process, delta-trace needs to be in a clean state:

```sh
# in /home/lan/src/cloned/gh/deltatraced/delta-trace
rm -rf * && git reset --hard
```

We clarified the IO errors some more,

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --release --bin app -- extract_old_format_records /home/lan/src/cloned/gh/deltatraced/delta-trace/

# out (error, relevant)
Failed to create new peripheral note: Io("/home/lan/src/cloned/gh/deltatraced/delta-trace/lan/topics/practice/ctf/topic/entries/2025/000 Solving Mountain and Dragon CTF/howtos/002 Start basic tcp server/client over terminal for testing.md", Os { code: 2, kind: NotFound, message: "No such file or directory" })
```

The issue is that entry name having a slash in it. We should fix that ourselves.

We also need to test `lan/topics/practice/ctf/topic/entries/2025/000 Solving Mountain and Dragon CTF.md` individually. 

It has much older spawning, different from the `From {block-identifier} in {spawner}` which we should change manually for conformity.

Spawn [[015 Update spawn convention for mountain and dragon CTF notes]] ^spawn-task-09e1ca

2025-10-07 Wk 41 Tue - 07:43 +03:00

Let's put all the content under `# Journal` for the new peripheral notes.

Editing `fn create_new_peripheral_note_from_old_format_entry`

```rust
// in fn create_new_peripheral_note_from_old_format_entry
crate::common::write_file_content(&format!("\n# Journal\n\n{}", entry.content), &peripheral_note_path)
	.map_err(|e| FnErr::Io(peripheral_note_path.clone(), e))?;
```

2025-10-07 Wk 41 Tue - 08:00 +03:00

- [ ] Update tutorial `lan/docs/2025/001 Note heading categories and method/` as it is out of date now

Noted in [[003 Objective reminders noted during sept 8 migration]]

2025-10-07 Wk 41 Tue - 08:06 +03:00

Spawn conventions will need to be updated for `lan/projects/2025/001 teensy2-tiny-piano/tasks/2025/000 Get teensy2 embedded system to work again under docker.md`

2025-10-07 Wk 41 Tue - 08:17 +03:00

We also had a single issue in `lan/topics/practice/ctf/2025/google-ctf-2025/postmortem/README.md`, we're just putting the single issue there as its own H1 instead of creating a cluster for a README.

2025-10-07 Wk 41 Tue - 08:19 +03:00

```sh
# in /home/lan/src/cloned/gh/deltatraced/delta-trace
git commit -m "cont. updating spawn conventions"                           

# out
[main 1d33163] cont. updating spawn conventions
 Date: Tue Oct 7 08:19:42 2025 +0300
 4 files changed, 19 insertions(+), 13 deletions(-)
```

2025-10-07 Wk 41 Tue - 08:26 +03:00

We gotta remember that headings for us here are autonumbered, so better autonumber also the added `Journal` H1 for peripherals

```rust
crate::common::write_file_content(&format!("\n# 1 Journal\n\n{}", entry.content), &peripheral_note_path)
	.map_err(|e| FnErr::Io(peripheral_note_path.clone(), e))?;
```

2025-10-07 Wk 41 Tue - 08:36 +03:00

Will need to update [gh Vincenius/wwebdev-comments #3](https://github.com/Vincenius/wwebdev-comments/issues/3) since it shares a link to changing notes. Make sure the link there becomes permanent.

Noted in [[003 Objective reminders noted during sept 8 migration]]

2025-10-07 Wk 41 Tue - 08:42 +03:00

```sh
# in /home/lan/src/cloned/gh/deltatraced/delta-trace
git commit -m "cont. updating spawn conventions"

# out
[main 73b2788] cont. updating spawn conventions
 7 files changed, 10 insertions(+), 46 deletions(-)
```

2025-10-07 Wk 41 Tue - 09:08 +03:00

Okay it looks good now. Let's commit the extraction results. There remains some issues like broken links, and peripherals not having the usual headings, but these can be fixed one by one on their own, prioritizing broken links.

```sh
# in /home/lan/src/cloned/gh/deltatraced/delta-trace
git commit -m "migration: turn mutli-context notes into note clusters"

# out (relevant)
[main d93ecde] migration: turn mutli-context notes into note clusters
 123 files changed, 5834 insertions(+), 5845 deletions(-)
```

2025-10-07 Wk 41 Tue - 09:21 +03:00

Let's renumber these. We will figure out the broken links regardless of the numbers, so now is the time to do it.

```sh
# in /home/lan/src/cloned/gh/deltatraced/delta-trace/lan/topics/practice/ctf/topic/entries/2025/000 Solving Mountain and Dragon CTF/tasks
mv '000 Run the game local with ability to modify the underlying script.md'                            '002 Run the game local with ability to modify the underlying script.md'                      
mv '001 Investigate fixing typescript errors from copies mountdrag.js.md'                              '003 Investigate fixing typescript errors from copies mountdrag.js.md'                        
mv '002 Build a frequency map of the commands being run and tape locations being hit on idle.md'       '004 Build a frequency map of the commands being run and tape locations being hit on idle.md' 
mv '003 Send web messages to terminal to collect experiment data.md'                                   '005 Send web messages to terminal to collect experiment data.md'                             
mv '004 Write python script to reconstruct tape until new undocumented command.md'                     '006 Write python script to reconstruct tape until new undocumented command.md'               
mv '005 Add program counter information to reconstructed tape to make jumps legible.md'                '007 Add program counter information to reconstructed tape to make jumps legible.md'          
mv '006 Handle loaded param16 flag automatically in commands when reconstructing tape program.md'      '008 Handle loaded param16 flag automatically in commands when reconstructing tape program.md'
mv '007 Reconstruct labels in the tape program.md'                                                     '009 Reconstruct labels in the tape program.md'                                               
mv '008 Create a driver to search for death and infinity strings.md'                                   '010 Create a driver to search for death and infinity strings.md'                             
mv '009 Reverse engineering the internal mini language.md'                                             '000 Reverse engineering the internal mini language.md'                                       
mv '010 Reconstructing tape content.md'                                                                '001 Reconstructing tape content.md'                                                          
```

2025-10-07 Wk 41 Tue - 09:25 +03:00

```sh
# in /home/lan/src/cloned/gh/deltatraced/delta-trace
git commit -m "renumbering mountain and dragon tasks"

# out (relevant)
[main f3c25c6] renumbering mountain and dragon tasks
 11 files changed, 0 insertions(+), 0 deletions(-)
```

2025-10-07 Wk 41 Tue - 11:41 +03:00

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
git commit -m "impl extracting of peripherals and complete it for delta trace"

# out
Trim Trailing Whitespace.................................................Passed
Check Yaml...........................................(no files to check)Skipped
Check for added large files..............................................Passed
Check formatting.........................................................Passed
Run tests................................................................Passed
Check clippy lints.......................................................Passed
[main 22c0b4d] impl extracting of peripherals and complete it for delta trace
 12 files changed, 683 insertions(+), 62 deletions(-)
```

