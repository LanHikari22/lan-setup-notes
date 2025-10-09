---
parent: '[[000 Note Repo Migration Sept 8]]'
spawned_by: '[[001 Parse through all notes and classify as core - peripheral - partial]]'
context_type: investigation
status: done
---

Parent: [000 Note Repo Migration Sept 8](../000%20Note%20Repo%20Migration%20Sept%208.md)

Spawned by: [001 Parse through all notes and classify as core - peripheral - partial](../tasks/001%20Parse%20through%20all%20notes%20and%20classify%20as%20core%20-%20peripheral%20-%20partial.md)

Spawned in: [<a name="spawn-invst-59092e" />^spawn-invst-59092e](../tasks/001%20Parse%20through%20all%20notes%20and%20classify%20as%20core%20-%20peripheral%20-%20partial.md#spawn-invst-59092e)

# 1 Journal

2025-09-16 Wk 38 Tue - 15:10 +03:00

````rust
pub fn process_markdown_files_in_vault(vault_folder: &ObsidianVaultPath, process_markdown_file: impl Fn(&PathBuf) -> Option<()>) {
    let working_items =
        cluster_note::get_working_item_paths_in_vault(vault_folder).expect("Failed to get working items");

    log::trace!("0");
    for item in working_items {
        log::trace!("1 {item:?}");
        match item {
            cluster_note::WorkingPath::Note(normal_note_file_path) => {
                process_markdown_file(&normal_note_file_path.path);
            },
            cluster_note::WorkingPath::ClusterFolder {
                cluster_root_folder: _cluster_root_folder,
                core_note_file: _core_note_file,
                category_folders_with_peripheral_files: _category_folders_with_peripheral_files,
            } => {
                process_markdown_file(&_core_note_file.path);
                _category_folders_with_peripheral_files
                    .into_iter()
                    .flat_map(|(_, peripheral_files)| peripheral_files)
                    .for_each(|peripheral_file| {
                        process_markdown_file(&peripheral_file.path);
                    })
            },
        }
        log::trace!("/1");
    }
    log::trace!("2");
}
````

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt004_old_format_record_list ~/src/cloned/gh/LanHikari22/lan-setup-notes

# out (relevant)
````

It gets stuck at `1` in cluster folder with core note `/home/lan/src/cloned/gh/LanHikari22/lan-setup-notes/lan/tasks/2025/000 Note Repo Migration Sept 8`.

Which is this!

2025-09-16 Wk 38 Tue - 15:23 +03:00

````rust
            cluster_note::WorkingPath::ClusterFolder {
                cluster_root_folder: _cluster_root_folder,
                core_note_file: _core_note_file,
                category_folders_with_peripheral_files: _category_folders_with_peripheral_files,
            } => {
                log::trace!("3");
                process_markdown_file(&_core_note_file.path);
                log::trace!("3.1");
                _category_folders_with_peripheral_files
                    .into_iter()
                    .flat_map(|(_, peripheral_files)| peripheral_files)
                    .for_each(|peripheral_file| {
                        log::trace!("4");
                        process_markdown_file(&peripheral_file.path);
                        log::trace!("/4");
                    });
                log::trace!("/3");
            },
        }
````

We just hit `3`.

In `expt004`,

````rust
fn main() {
    let vault_folder = drivers::init_logging_and_get_obsidian_vault();
    let opt_note_path = drivers::get_opt_arg_note_path();

    let process_markdown_file = |path: &PathBuf, only_summarize: bool| -> Option<()> {
        log::trace!("A");
        let content = common::read_file_content(path)
            .expect("Could not read content");
        log::trace!("AA");

        let events = common::parse_markdown_file(&content);
        log::trace!("AAA");

        let old_format_records = cluster_note::get_note_old_format_entries(&events)?;
        log::trace!("B");

        let records_with_extracts = old_format_records
            .iter()
            .map(|entry| (
                entry,
                common::extract_linkable_obsidian_md_items(&entry.events),
                common::extract_obsidian_md_links(&entry.events),
            ))
            .collect::<Vec<_>>();

        if records_with_extracts.len() != 0 {
            log::trace!("path {path:?} has {} records", records_with_extracts.len());
        }

        log::trace!("C");

        for (record, linkables, links) in records_with_extracts {
            log::trace!("D");
            let spawn_metadata = cluster_note::extract_spawn_metadata_from_old_format(&linkables, &links);
            log::trace!("D1");

            log::trace!("record {:?} of {:?} has {} events.", record.entry_name, record.entry_type, record.events.len());
            log::trace!("\tIt has {} linkables, {} links, and {} spawn items.", linkables.len(), links.len(), spawn_metadata.len());

            if !only_summarize {
                log::trace!("record: {record:?}");
                log::trace!("linkables: {linkables:?}");
                log::trace!("links: {links:?}");
                log::trace!("spawn_metadata: {spawn_metadata:?}");
            }
            log::trace!("/D");
        }
        log::trace!("/C");

        Some(())
    };

    log::trace!("E");
    match opt_note_path {
        Some(note_path) => {
            process_markdown_file(&note_path, true);
        },
        None => {
            drivers::process_markdown_files_in_vault(&vault_folder, |path| process_markdown_file(path, false));
        },
    }
    log::trace!("/E");
}
````

We hit `3AAA`.

So we're frozen at

````rust
let old_format_records = cluster_note::get_note_old_format_entries(&events)?;
````

2025-09-16 Wk 38 Tue - 15:36 +03:00

It was because we used not to advance the cursor when no sub H2 events are found like in this fix:

````rust
if mut_inner_cur - mut_cur > 0 {
	mut_grouped_events
		.push(Grouped::SubH2(&events[mut_cur..mut_inner_cur]));
	mut_cur = mut_inner_cur;
} else {
	mut_cur += 1;
}
````

So there might be cases where we don't register any SubH2s?

2025-09-16 Wk 38 Tue - 20:43 +03:00

````rust
if mut_inner_cur - mut_cur > 0 {
	mut_grouped_events
		.push(Grouped::SubH2(&events[mut_cur..mut_inner_cur]));
	mut_cur = mut_inner_cur;
} else {
	log::trace!("{:?}", events[mut_cur]);
	mut_cur += 0;
}
````

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt004_old_format_record_list ~/src/cloned/gh/LanHikari22/lan-setup-notes

# out
[2025-09-16 20:42:56 TRACE src/cluster_note.rs:573] Start(Heading { level: H2, id: None, classes: [], attrs: [] })
[infinite ...]
````

So we encounter an `H2`, at the current cursor, so why are we not handling that instead of sub `H2` items?

````rust
while mut_cur < events.len() {
	log::trace!("0A mut_cur {mut_cur} -> {:?}", events[mut_cur]);
	log::trace!("0B {:?}", comm::process_heading_event_of_level(&HeadingLevel::H1, &events[mut_cur..]));
	log::trace!("0C {:?}", comm::process_heading_event_of_level(&HeadingLevel::H2, &events[mut_cur..]));
	match comm::process_heading_event_of_level(&HeadingLevel::H1, &events[mut_cur..]) {
	
// [...]
if mut_inner_cur - mut_cur > 0 {
	mut_grouped_events
		.push(Grouped::SubH2(&events[mut_cur..mut_inner_cur]));
	mut_cur = mut_inner_cur;
} else {
	log::trace!("2 {:?}", events[mut_cur]);
	mut_cur += 0;
}
````

````
[2025-09-16 20:58:05 TRACE src/cluster_note.rs:523] 0A mut_cur 1 -> Start(Heading { level: H2, id: None, classes: [], attrs: [] })
[2025-09-16 20:58:05 TRACE src/cluster_note.rs:524] 0B None
[2025-09-16 20:58:05 TRACE src/cluster_note.rs:525] 0C None
[2025-09-16 20:58:05 TRACE src/cluster_note.rs:577] 1 Start(Heading { level: H2, id: None, classes: [], attrs: [] })
[infinite ...]
````

Even though it's an `H2`, `process_heading_event_of_level(H2)` won't recognize it!

2025-09-16 Wk 38 Tue - 21:03 +03:00

Let's make the errors for this more explicit rather than just None.

2025-09-16 Wk 38 Tue - 21:17 +03:00

````
[2025-09-16 21:26:24 TRACE src/cluster_note.rs:523] 0A mut_cur 1 -> Start(Heading { level: H2, id: None, classes: [], attrs: [] })
[2025-09-16 21:26:24 TRACE src/cluster_note.rs:524] 0B Err(WrongLevel(H1, H2))
[2025-09-16 21:26:24 TRACE src/cluster_note.rs:525] 0C Err(InvalidScheme(2, "SoftBreak"))
[2025-09-16 21:26:24 TRACE src/cluster_note.rs:577] 1 Start(Heading { level: H2, id: None, classes: [], attrs: [] })

[infinite ...]
````

Invalid scheme... Is our assumption of `H<N> Text /H<N>` invalid?

2025-09-16 Wk 38 Tue - 21:41 +03:00

We're creating `expt005` to reproduce some events from input markdown with [gh pulldown-cmark/pulldown-cmark](https://github.com/pulldown-cmark/pulldown-cmark/).

2025-09-16 Wk 38 Tue - 21:55 +03:00

It seems this is happening in `"/home/lan/src/cloned/gh/LanHikari22/lan-setup-notes/lan/tasks/2025/000 Note Repo Migration Sept 8/000 Note Repo Migration Sept 8.md"`

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt000_parse_single_pulldown_cmark ~/src/cloned/gh/LanHikari22/lan-setup-notes/lan/tasks/2025/000\ Note\ Repo\ Migration\ Sept\ 8/000\ Note\ Repo\ Migration\ Sept\ 8.md | less
````

It's in the very beginning.

````
Event Rule
Event Start(Heading { level: H2, id: None, classes: [], attrs: [] })
Event Text(Boxed("deprecates: \"[[001 Looking into heading level graph views]]\""))
Event SoftBreak
Event Text(Boxed("breaks: \"[[000 Setting up time logging in Obsidian]]\""))
Event End(Heading(H2))
````

That's the frontmatter. It's acting as if it's an H2.

It's reproduced with `expt005`.

````sh
cargo run --bin expt005_repro_heading_markdown_events
````

````rust
/*
	Event Rule
	Event Start(Heading { level: H2, id: None, classes: [], attrs: [] })
	Event Text(Boxed("deprecates: \"[[001 Looking into heading level graph views]]\""))
	Event SoftBreak
	Event Text(Boxed("breaks: \"[[000 Setting up time logging in Obsidian]]\""))
	Event End(Heading(H2))
 */
print_events(
	&r#"
	@ ---
	@ deprecates: "[[001 Looking into heading level graph views]]"
	@ breaks: "[[000 Setting up time logging in Obsidian]]"
	@ ---
	@ "#
	.replace("@ ","")
	.replace("        ","")
	,
);
````

2025-09-16 Wk 38 Tue - 22:08 +03:00

This should mean that this is working, it's just that we should be skipping this, as we have been. It's not an H2, and it's also not a subH2 after all.

2025-09-16 Wk 38 Tue - 22:16 +03:00

Okay so we are not always in a `SubH2` just because we fail to parse H1 or H2.

Since a frontmatter will only appear in the beginning of the file, we can just increment in case we haven't found any groups yet, since it's impossible to find a `SubH2` before an `H2` anyway.

2025-09-16 Wk 38 Tue - 22:20 +03:00

````rust
if mut_inner_cur - mut_cur > 0 {
	mut_grouped_events
		.push(Grouped::SubH2(&events[mut_cur..mut_inner_cur]));
	mut_cur = mut_inner_cur;
} else {
	// We should not get here. This should've been handled by H1 or H2.
	log::warn!("Could not get any content within H2: {:?}", &events[mut_cur..mut_cur+min(5, events.len())]);
	mut_cur += 1;
}
````

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt004_old_format_record_list ~/src/cloned/gh/LanHikari22/lan-setup-notes

# out (relevant)
[2025-09-16 22:26:08 WARN src/cluster_note.rs:575] Could not get any content within H2: [Start(Heading { level: H2, id: None, classes: [], attrs: [] }), Text(Borrowed("6.4 Issues with ")), Start(Link { link_type: Inline, dest_url: Borrowed("https://github.com/LanHikari22/lan-exp-scripts/blob/main/scripts/2025/Wk27-000-expedition-33-ui-auto/test.py"), title: Borrowed(""), id: Borrowed("") }), Text(Borrowed("ydotool cv test script")), End(Link)]
[2025-09-16 22:26:08 WARN src/cluster_note.rs:575] Could not get any content within H2: [Start(Heading { level: H2, id: None, classes: [], attrs: [] }), Text(Borrowed("7.1 On ")), Start(Link { link_type: Inline, dest_url: Borrowed("https://www.gtk.org/"), title: Borrowed(""), id: Borrowed("") }), Text(Borrowed("GTK")), End(Link)]
[2025-09-16 22:26:08 WARN src/cluster_note.rs:575] Could not get any content within H2: [Start(Heading { level: H2, id: None, classes: [], attrs: [] }), Text(Borrowed("4.1 (~1) Create ")), Code(Borrowed("open_timeline_log.js")), End(Heading(H2)), Start(Paragraph)]
````

It happens three times.

Headings with links in them! And Code!

2025-09-16 Wk 38 Tue - 22:31 +03:00

In `expt005`,

````rust
    /*
        Event Start(Heading { level: H2, id: None, classes: [], attrs: [] })
        Event Text(Borrowed("This heading has "))
        Event Start(Link { link_type: Inline, dest_url: Borrowed("https://www.google.com"), title: Borrowed(""), id: Borrowed("") })
        Event Text(Borrowed("links"))
        Event End(Link)
        Event Text(Borrowed(" and "))
        Event Code(Borrowed("code"))
        Event Text(Borrowed(" in it! "))
        Event Code(Borrowed("Many"))
        Event Text(Borrowed(" "))
        Event Code(Borrowed("of"))
        Event Text(Borrowed(" "))
        Event Code(Borrowed("them"))
        Event Text(Borrowed("!"))
        Event End(Heading(H2))
     */
    print_events(
        &r#"
        @ ## This heading has [links](https://www.google.com) and `code` in it! `Many` `of` `them`!
        @ "#
        .replace("@ ","")
        .replace("        ","")
        ,
    );
````

This really breaks our assumption here.

2025-09-16 Wk 38 Tue - 22:41 +03:00

But those will not work for our migration. Let's remove these use cases!

2025-09-16 Wk 38 Tue - 22:47 +03:00

Okay, so we resolved the termination issue, and removed the weird headings that won't migrate well. There is still the fact that we're not finding any old format headings:

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt004_old_format_record_list ~/src/cloned/gh/LanHikari22/lan-setup-notes

# out (relevent)
[nothing]
````
