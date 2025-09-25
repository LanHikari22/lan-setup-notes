---
parent: '[[000 Note Repo Migration Sept 8]]'
spawned_by: '[[001 How does obsidian-export parse markdown in postprocessors?]]'
context_type: task
status: done
---

Parent: [000 Note Repo Migration Sept 8](../000%20Note%20Repo%20Migration%20Sept%208.md)

Spawned by: [001 How does obsidian-export parse markdown in postprocessors?](../investigations/001%20How%20does%20obsidian-export%20parse%20markdown%20in%20postprocessors%3F.md)

Spawned in: [<a name="spawn-task-0d2c20" />^spawn-task-0d2c20](../investigations/001%20How%20does%20obsidian-export%20parse%20markdown%20in%20postprocessors%3F.md#spawn-task-0d2c20)

# 1 Journal

2025-09-09 Wk 37 Tue - 15:25 +03:00

You can find the library [here](https://github.com/pulldown-cmark/pulldown-cmark/).

Let's build on the project we created in [001 Parse through all notes and classify as core - peripheral - partial](001%20Parse%20through%20all%20notes%20and%20classify%20as%20core%20-%20peripheral%20-%20partial.md)

````sh
# in /home/lan/src/cloned/gh/LanHikari22/lan-exp-scripts/scripts/2025/weekly/Wk37-000-obsidian-vault-migration-rs/migration_rs
cargo add pulldown-cmark
````

2025-09-09 Wk 37 Tue - 15:48 +03:00

Checked `tutorials/2025/topics/db/diesel.rs/getting-started/diesel_demo/src/bin/get_post.rs` for basic cli argument parsing

Checked `~/src/cloned/gh/LanHikari22/lan_rs_common/src/util/io.rs` for file IO

2025-09-09 Wk 37 Tue - 16:04 +03:00

We can now run

````sh
# in /home/lan/src/cloned/gh/LanHikari22/lan-exp-scripts/scripts/2025/weekly/Wk37-000-obsidian-vault-migration-rs/migration_rs
cargo run --bin ex000_parse_single_pulldown_cmark ~/src/cloned/gh/LanHikari22/lan-setup-notes/README.md
````

and get all the pulldown events printed for that speciifc markdown file.

We will see headings like this, which are of interest for [our classification task](001%20Parse%20through%20all%20notes%20and%20classify%20as%20core%20-%20peripheral%20-%20partial.md):

````
Event Start(Heading { level: H1, id: None, classes: [], attrs: [] })
Event Text(Borrowed("1 What is this?"))
Event End(Heading(H1))
````
