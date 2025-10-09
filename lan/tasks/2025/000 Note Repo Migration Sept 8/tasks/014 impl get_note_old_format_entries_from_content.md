---
parent: '[[000 Note Repo Migration Sept 8]]'
spawned_by: '[[013 Change all delta-trace old format notes into note clusters without applying link fixing]]'
context_type: task
status: todo
---

Parent: [000 Note Repo Migration Sept 8](../000%20Note%20Repo%20Migration%20Sept%208.md)

Spawned by: [013 Change all delta-trace old format notes into note clusters without applying link fixing](013%20Change%20all%20delta-trace%20old%20format%20notes%20into%20note%20clusters%20without%20applying%20link%20fixing.md)

Spawned in: [<a name="spawn-task-0a71ba" />^spawn-task-0a71ba](013%20Change%20all%20delta-trace%20old%20format%20notes%20into%20note%20clusters%20without%20applying%20link%20fixing.md#spawn-task-0a71ba)

# 1 Objective

We need to split a file into several text blobs, some remain in the file and others are entries.

# 2 Related

Consequence of judgment [000 To perform the file-level old record transformations for migration using low level string splitting](../judgments/000%20To%20perform%20the%20file-level%20old%20record%20transformations%20for%20migration%20using%20low%20level%20string%20splitting.md)

# 3 Journal

2025-10-06 Wk 41 Mon - 12:56 +03:00

````rust
// in fn get_note_old_format_entries
if !OLD_FORMAT_HEADINGS.contains(&strip_autonumbered_sections(heading1).trim())
````

We need to make sure to remember to strip autonumbers, but the first step is to break down the entire file content into anything... (H1 category ...), anything

Categories flip based on the latest `H1` identified.

2025-10-06 Wk 41 Mon - 13:13 +03:00

The algorithm was to first split the file events into `H1/H2/Content` event groups, filter that for relevant groups, and finally process the entries sequentially with relevant `H1` being the category, relevant `H2` being the entry, and relevant `Content` being the content of that entry.

We can do this but with line manipulation, and by filtering out all relevant groups, we also get the remaining which stays in the core note file.

2025-10-06 Wk 41 Mon - 13:21 +03:00

We need to handle whether we're in special text modes like codeblock.

2025-10-06 Wk 41 Mon - 13:58 +03:00

2025-10-06 Wk 41 Mon - 16:32 +03:00

Apparently having this in multiple test files can cause tests to fail sometimes

````rust
pub fn init() {
	G_INIT_ONCE.call_once(|| {
		crate::drivers::init_logging_with_level_or_fail(log::LevelFilter::Trace);
	});
}
````

So changing it to just try to init logging but not fail.

2025-10-06 Wk 41 Mon - 16:41 +03:00

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo test --lib


# out (error, relevant)
<given>
# AAA
Some text...
More text...
## AAA.0
More text...
</given>

<expected>
# AAA
Some text...
More text...
## AAA.0
More text...
</expected>

<actual>
# AAA
Some text...
More text...
## AAA.0
</actual>

thread 'cluster_note::tests::test_get_note_old_format_entries_from_content' panicked at src/cluster_note.rs:1157:17:
case-000: Remaining is not as expected
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
````

````
[2025-10-06T13:49:22Z TRACE migration_rs::cluster_note] grouped_lines: [H1("# AAA"), Content(["Some text...", "More text..."]), H2("## AAA.0")]                                               
[2025-10-06T13:49:22Z TRACE migration_rs::cluster_note] entry_grouped_lines: []                                                                                                               
[2025-10-06T13:49:22Z TRACE migration_rs::cluster_note] non_entry_grouped_lines: [H1("# AAA"), Content(["Some text...", "More text..."]), H2("## AAA.0")]                                     
[2025-10-06T13:49:22Z TRACE migration_rs::cluster_note] old_format_records: []
````

2025-10-06 Wk 41 Mon - 16:54 +03:00

This is because when we're done processing lines, there's content we have not yet pushed to the result, so this should fix it:

After the for loop:

````rust
// in fn get_note_old_format_entries_from_content
if mut_last_content_group.len() != 0 {
	mut_grouped_lines.push(Grouped::Content(mut_last_content_group.clone()));
}
````

2025-10-06 Wk 41 Mon - 16:56 +03:00

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo test --lib

# out
[OK]
````

Ok let's add another test case

2025-10-06 Wk 41 Mon - 17:13 +03:00

The next failure on `case-001` is because we're not registering entries which have 0 content, so it asserts on two entries found instead of 3.

2025-10-07 Wk 41 Tue - 03:46 +03:00

````rust
OldFormatEntryTestData {
	name: "case-003",
	given: r#"
			@ ## Tasks
			@ # Ideas
			@ # Investigations
			@ # Issues
			@ ```
			@ # HowTos
			@ ```
		"#
	.trim()
	.replace("@ ", "")
	.replace("@", "")
	.replace("                        ", ""),
	expected_remaining: r#"
			@ ## Tasks
			@ ```
			@ # HowTos
			@ ```
		"#
	.trim()
	.replace("@ ", "")
	.replace("@", "")
	.replace("                        ", ""),
	expected_entries: vec![],
},
````

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo test --lib

# out (error, relevant)
test cluster_note::tests::test_get_note_old_format_entries_from_content ... FAILED
case-003: Failed to get entries: EventTypeAndNameNotConfigured
````

2025-10-07 Wk 41 Tue - 03:51 +03:00

Improving the error information a bit gives us

````
case-003: Failed to get entries: EventTypeAndNameNotConfigured(3, "```")
````

This refers to

````
@ ```
@ # HowTos
@ ```
````

It is content that was found, and assigned relevant because it comes after `# Issues`, and yet there was no record to which it belonged. Our assumption is that content is always found inside H2 entries inside H1 categories, until an H1 outside the old format breaks this pattern, so in this case the test has to change.

2025-10-07 Wk 41 Tue - 04:03 +03:00

This gives `OK`:

````rust
OldFormatEntryTestData {
	name: "case-003",
	given: r#"
			@ ## Tasks
			@ # Ideas
			@ # Investigations
			@ # Issues
			@ # No more Entries
			@ ```
			@ # HowTos
			@ ```
		"#
	.trim()
	.replace("@ ", "")
	.replace("@", "")
	.replace("                        ", ""),
	expected_remaining: r#"
			@ ## Tasks
			@ # No more Entries
			@ ```
			@ # HowTos
			@ ```
		"#
	.trim()
	.replace("@ ", "")
	.replace("@", "")
	.replace("                        ", ""),
	expected_entries: vec![],
},
````

Let's capturing the previous `case-003` as `case-003-000-fail`  to signify that this failure is expected.

2025-10-07 Wk 41 Tue - 04:28 +03:00

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --release --bin app -- extract_old_format_records /home/lan/src/cloned/gh/deltatraced/delta-trace/

# out (error, relevant)
[2025-10-07T01:39:18Z INFO  app] value_path: ObsidianVaultPath { path: "/home/lan/src/cloned/gh/deltatraced/delta-trace/" }
[2025-10-07T01:39:18Z INFO  app] processing "/home/lan/src/cloned/gh/deltatraced/delta-trace/lan/topics/read/papers/ai/entries/2025/000 Attention is all you need.md"

thread 'main' panicked at src/bin/app.rs:100:14:
Failed to process old format entries from content: EventTypeAndNameNotConfigured(1, "")
````

2025-10-07 Wk 41 Tue - 04:46 +03:00

We're able to reproduce this issue with empty lines with `case-004`,

````rust
OldFormatEntryTestData::Pass {
	name: "case-004",
	given: r#"
			@ # Journal
			@ 
			@ # Tasks
			@ 
			@ ## Water plants
			@ 
			@ There are many plants to water!
			@ 
			@ # References
			@ 1. Your favorite dictionary
		"#
	.trim()
	.replace("@ ", "")
	.replace("@", "")
	.replace("                        ", ""),
	expected_remaining: r#"
			@ # Journal
			@ 
			@ # References
			@ 1. Your favorite dictionary
		"#
	.trim()
	.replace("@ ", "")
	.replace("@", "")
	.replace("                        ", ""),
	expected_entries: vec![
		OldFormatEntryContent {
			entry_type: OldFormatEntryType::Task,
			entry_name: "Water plants".to_string(),
			content: r#"
			@ 
			@ There are many plants to water!
			@ 
		"#
			.trim()
			.replace("@ ", "")
			.replace("@", "")
			.replace("                        ", ""),
		},
	],
},
````

It is failing likely because it is finding content inside `# Task` but not inside an entry in `# Task`. This is likely to happen due to whitespace. Let's handle that case explicitly.
