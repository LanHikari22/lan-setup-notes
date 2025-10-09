---
parent: '[[000 Note Repo Migration Sept 8]]'
context_type: task
status: todo
---

Parent: [000 Note Repo Migration Sept 8](../000%20Note%20Repo%20Migration%20Sept%208.md)

# 1 Objective

Create a script that takes an obsidian vault path as input, and outputs classifications for every note encountered, including relevant metadata in a separate file.

So it outputs a classification file, and a metadata file for additional data per file.

# 2 Journal

2025-09-09 Wk 37 Tue - 14:44 +03:00

Let's do this in rust and put it in [lan-exp-scripts](https://github.com/LanHikari22/lan-exp-scripts).

````sh
# in /home/lan/src/cloned/gh/LanHikari22/lan-exp-scripts/scripts/2025/weekly/Wk37-000-obsidian-vault-migration-rs/migration_rs
cargo init
````

2025-09-09 Wk 37 Tue - 14:50 +03:00

Spawn [000 How should we parse markdown files in Rust?](../investigations/000%20How%20should%20we%20parse%20markdown%20files%20in%20Rust%3F.md) <a name="spawn-invst-38f502" />^spawn-invst-38f502

2025-09-09 Wk 37 Tue - 16:07 +03:00

From [002 Parse a single obsidian markdown file with pulldown cmark](002%20Parse%20a%20single%20obsidian%20markdown%20file%20with%20pulldown%20cmark.md),

We can now parse markdown events for any markdown file via the common mark format.

Spawn [000 Break down lan-exp-scripts into an org](../entries/000%20Break%20down%20lan-exp-scripts%20into%20an%20org.md) <a name="spawn-entry-e65ac0" />^spawn-entry-e65ac0

2025-09-09 Wk 37 Tue - 16:52 +03:00

Now that we have [deltaarchives](https://github.com/deltachives), let's put our project there instead.

2025-09-09 Wk 37 Tue - 17:39 +03:00

The project is in [gh deltachives/2025-Wk37-000-obsidian-migration](https://github.com/deltachives/2025-Wk37-000-obsidian-migration).

2025-09-09 Wk 37 Tue - 16:09 +03:00

2025-09-09 Wk 37 Tue - 18:15 +03:00

The new format index categories: `Entries, HowTos, Ideas, Inferences, Investigations, Issues, Tasks`

The old format heading categories: `Tasks, Issues, HowTos, Investigations, Ideas, Side Notes`

* big note
  * Exists within a folder of the same name
  * Contains an `# Index` `H1` heading with the new format index categories as `H2` subheadings.
* small note
  * Exists within a category of a big note folder
  * Has exactly one context. No old format entry headings, and no Index heading.
* hybrid note
  * This is the old format. It is a file that is not within a folder of the same name, and also it contains some H1 headings from the old format heading categories.
  * It contains at least one entry.
* small hybrid note
  * This is a note that may contain some of the old format heading categories, but contains no entries in them. This can changed into just a file, rather than a big note folder.

2025-09-09 Wk 37 Tue - 18:25 +03:00

Checked `rs_comm::io::glob_multiple_file_formats_in_path`.

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo add itertools
````

2025-09-09 Wk 37 Tue - 18:47 +03:00

Spawn [003 Make everything under lan_rs_common featured for very minimal includes](003%20Make%20everything%20under%20lan_rs_common%20featured%20for%20very%20minimal%20includes.md) <a name="spawn-task-d829aa" />^spawn-task-d829aa

2025-09-09 Wk 37 Tue - 23:12 +03:00

Spawn [001 Specifying arguments and returns in rust function docs](../howtos/001%20Specifying%20arguments%20and%20returns%20in%20rust%20function%20docs.md) <a name="spawn-howto-6ff1c8" />^spawn-howto-6ff1c8

2025-09-10 Wk 37 Wed - 01:01 +03:00

Spawn [002 Copying folder structure but modifying some files in Rust](../howtos/002%20Copying%20folder%20structure%20but%20modifying%20some%20files%20in%20Rust.md) <a name="spawn-howto-9a69d0" />^spawn-howto-9a69d0

2025-09-13 Wk 37 Sat - 16:05 +03:00

We changed the names! Now it's one core note and many peripheral notes with context types all inside a note cluster!

Situation is also a bit more complicated because we have partial clusters now which we need to classify. Those are clusters whose core note still has old format records in it. Besides that, let's get to classifying!

2025-09-13 Wk 37 Sat - 16:22 +03:00

Spawn [002 What's a directory entry from folder.read_dir in rust?](../investigations/002%20What's%20a%20directory%20entry%20from%20folder.read_dir%20in%20rust%3F.md) <a name="spawn-invst-c00179" />^spawn-invst-c00179

2025-09-13 Wk 37 Sat - 19:37 +03:00

Renaming `exNNN` binaries to `exptNNN` to denote they're experiments not example usage of the code.

2025-09-13 Wk 37 Sat - 19:44 +03:00

Spawn [003 Investigating frontmatter to markdown event mappings with pulldown_cmark](../investigations/003%20Investigating%20frontmatter%20to%20markdown%20event%20mappings%20with%20pulldown_cmark.md) <a name="spawn-invst-3d5e66" />^spawn-invst-3d5e66

2025-09-13 Wk 37 Sat - 20:00 +03:00

Let's parse `Rule H2 (Text [Softbreak] ...) /H2` at the beginning of the file for frontmatter.

2025-09-13 Wk 37 Sat - 20:17 +03:00

Checked [stackoverflow answer](https://stackoverflow.com/a/51429606/6944447) for `matches!` for enum variants

2025-09-13 Wk 37 Sat - 21:18 +03:00

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo add tap
````

Need this for `.pipe` to not have to make unnecessary intermediate variables or have unreadable compute chains.

2025-09-13 Wk 37 Sat - 21:34 +03:00

So for the intent of this migration, we're really only interested in modifying either individual markdown files at some arbitrary path, or cluster note folders at some path. When we make modifications, we inherit the path of either those two things. So let's just retrieve all the working items given a vault folder path. And we chose to do in-place modification, make sure to back up before running that.

2025-09-13 Wk 37 Sat - 23:19 +03:00

(update)
Let's make sure a valid vault path includes an `.obsidian/` folder and make sure to skip that folder.

2025-09-14 Wk 37 Sun - 02:05 +03:00

Even though we're not skipping `.obsidian/` there aren't any markdown files there so it isn't currently an issue. This might be a safe assumption.

(/update)

Implemented. Now let's create an experiment binary to show all working items of a vault.

2025-09-13 Wk 37 Sat - 23:40 +03:00

Spawn [004 Debugging through migration expt003 errors](../investigations/004%20Debugging%20through%20migration%20expt003%20errors.md) <a name="spawn-invst-0f35dc" />^spawn-invst-0f35dc

2025-09-14 Wk 37 Sun - 01:41 +03:00

Now we have all the working items of consideration! This on its own does a lot of classification for us. normal notes, cluster folders, core notes, category folders, and peripheral notes are all recognized via

````rust
pub enum WorkingPath {
    Note(NormalNoteFilePath),
    ClusterFolder{
        cluster_root_folder: ClusterRootFolderPath,
        core_note_file: CoreNoteFilePath,
        category_folders_with_peripheral_files: Vec<(ClusterCategoryFolderPath, Vec<PeripheralNoteFilePath>)>,
    }
}
````

What remains is to classify either normal notes or core notes further. normal notes can be clusters in one file as per the old format, and current core notes can be partial. that is, they still contain some entries within them as per the old format.

2025-09-14 Wk 37 Sun - 03:05 +03:00

As long as we can extract the old format records, we can tell that a note is of the old format if they contain them. If that happens to be a core file also, then we know there's partial core.

2025-09-14 Wk 37 Sun - 03:37 +03:00

We laid out the functions for the IO layer to do the migration.

2025-09-14 Wk 37 Sun - 03:46 +03:00

Spawn [005 Determining pulldown cmark events for old entries](../investigations/005%20Determining%20pulldown%20cmark%20events%20for%20old%20entries.md) <a name="spawn-invst-7d671c" />^spawn-invst-7d671c

2025-09-14 Wk 37 Sun - 13:22 +03:00

For this, we will make a corresponding experiment `expt004` that lists the names, and number of events as well as existing headings + block identifiers for each entry in an old format note.

2025-09-14 Wk 37 Sun - 13:52 +03:00

Created `drivers.rs` to simplify the experiment code and keep it from getting repetitive. This has common driver patterns needed, like quickly setting up logging and requesting vault repo path from user via args.

2025-09-14 Wk 37 Sun - 15:01 +03:00

`expt004` is written to optionally take a note path in addition to the vault, to show more information on the objects extracted.

2025-09-15 Wk 38 Mon - 02:25 +03:00

I was getting an error on

````rust
pub fn get_note_old_format_entries<'a>(events: &Vec<Event<'a>>) -> Option<Vec<OldFormatEntry<'a>>> {
	// ...

	Some(old_format_entries)
//  ^^^^^^^^^^^^^^^^^^^^^^^^	
}
````

````
error[E0621]: explicit lifetime required in the type of `events`
   --> src/cluster_note.rs:676:5
    |
676 |     Some(old_format_entries)
    |     ^^^^^^^^^^^^^^^^^^^^^^^^ lifetime `'a` required
    |
help: add explicit lifetime `'a` to the type of `events`
    |
548 | pub fn get_note_old_format_entries<'a>(events: &'a Vec<Event<'a>>) -> Option<Vec<OldFormatEntry<'a>>> {
    |                                                 ++
````

which is fixed by making `events: &Vec` `events: &'a Vec` in the argument.

2025-09-15 Wk 38 Mon - 18:22 +03:00

We should look into some more robust error handling practices.

For example we learned from [gh Utagai/shi](https://github.com/Utagai/shi) about the [thiserror](https://docs.rs/thiserror/latest/thiserror/) crate for preventing dual locations of error string and error enum. We can use this.

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo add thiserror
````

2025-09-15 Wk 38 Mon - 19:12 +03:00

[thiserror](https://docs.rs/thiserror/latest/thiserror/) also recommends [gh dtolnay/anyhow](https://github.com/dtolnay/anyhow) for easy error propagation.

2025-09-15 Wk 38 Mon - 19:16 +03:00

I ran into this pattern multiple times where I have a list `[A, B, C, D, B', E, F] ` and I want to turn it into groups by startpoint `B`, and endpoint `B'` to turn into `[[A], [B, C, D, B'], [E, F]]`

It happened with the `H1 Text /H1`  event handling, and now also with a string that may have many `[[link]]` s in it. Though for the link one, I've decided to just parse valid links at every char here. This would work because they cannot intersect, and would handle potential bracket imbalances.

2025-09-15 Wk 38 Mon - 20:34 +03:00

There's a lot of variance for how spawns are written. Let's try to fix this manually.

Spawn [008 Search for and fix old format Spawn strings for easier migration](008%20Search%20for%20and%20fix%20old%20format%20Spawn%20strings%20for%20easier%20migration.md) <a name="spawn-task-22c0e7" />^spawn-task-22c0e7

2025-09-16 Wk 38 Tue - 15:05 +03:00

Spawn [006 Debugging through expt004 not terminating](../investigations/006%20Debugging%20through%20expt004%20not%20terminating.md) <a name="spawn-invst-59092e" />^spawn-invst-59092e

2025-09-16 Wk 38 Tue - 22:48 +03:00

Spawn [007 Debugging no old format records found in expt004](../investigations/007%20Debugging%20no%20old%20format%20records%20found%20in%20expt004.md) <a name="spawn-invst-db4910" />^spawn-invst-db4910

2025-09-17 Wk 38 Wed - 03:43 +03:00

Let's jump right to the migration itself now.
