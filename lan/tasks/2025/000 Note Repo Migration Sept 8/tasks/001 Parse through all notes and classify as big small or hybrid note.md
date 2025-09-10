---
parent: "[[000 Note Repo Migration Sept 8]]"
---
Parent: [[000 Note Repo Migration Sept 8]]

# 1 Objective

Create a script that takes an obsidian vault path as input, and outputs classifications for every note encountered, including relevant metadata in a separate file. 

So it outputs a classification file, and a metadata file for additional data per file.

# 2 Journal

2025-09-09 Wk 37 Tue - 14:44 +03:00

Let's do this in rust and put it in [lan-exp-scripts](https://github.com/LanHikari22/lan-exp-scripts).

```sh
# in /home/lan/src/cloned/gh/LanHikari22/lan-exp-scripts/scripts/2025/weekly/Wk37-000-obsidian-vault-migration-rs/migration_rs
cargo init
```

2025-09-09 Wk 37 Tue - 14:50 +03:00

Spawn [[000 How should we parse markdown files in Rust?]] ^spawn-invst-38f502

2025-09-09 Wk 37 Tue - 16:07 +03:00

From [[002 Parse a single obsidian markdown file with pulldown cmark]],

We can now parse markdown events for any markdown file via the common mark format.

Spawn [[000 Break down lan-exp-scripts into an org]] ^spawn-entry-e65ac0

2025-09-09 Wk 37 Tue - 16:52 +03:00

Now that we have [deltaarchives](https://github.com/deltachives), let's put our project there instead.

2025-09-09 Wk 37 Tue - 17:39 +03:00

The project is in [gh deltachives/2025-Wk37-000-obsidian-migration](https://github.com/deltachives/2025-Wk37-000-obsidian-migration).

2025-09-09 Wk 37 Tue - 16:09 +03:00

2025-09-09 Wk 37 Tue - 18:15 +03:00

The new format index categories: `Entries, HowTos, Ideas, Inferences, Investigations, Issues, Tasks`

The old format heading categories: `Tasks, Issues, HowTos, Investigations, Ideas, Side Notes`

- big note
	- Exists within a folder of the same name
	- Contains an `# Index` `H1` heading with the new format index categories as `H2` subheadings.
- small note
	- Exists within a category of a big note folder
	- Has exactly one context. No old format entry headings, and no Index heading.
- hybrid note
	- This is the old format. It is a file that is not within a folder of the same name, and also it contains some H1 headings from the old format heading categories.
	- It contains at least one entry.
- small hybrid note
	- This is a note that may contain some of the old format heading categories, but contains no entries in them. This can changed into just a file, rather than a big note folder.

2025-09-09 Wk 37 Tue - 18:25 +03:00

Checked `rs_comm::io::glob_multiple_file_formats_in_path`. 

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo add itertools
```

2025-09-09 Wk 37 Tue - 18:47 +03:00

Spawn [[003 Make everything under lan_rs_common featured for very minimal includes]] ^spawn-task-d829aa

2025-09-09 Wk 37 Tue - 23:12 +03:00

Spawn [[001 Specifying arguments and returns in rust function docs]] ^spawn-howto-6ff1c8

2025-09-10 Wk 37 Wed - 01:01 +03:00

Spawn [[002 Copying folder structure but modifying some files in Rust]] ^spawn-howto-9a69d0

