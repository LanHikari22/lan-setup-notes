---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[001 How does obsidian-export parse markdown in postprocessors?]]"
context_type: task
status: done
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned in [[001 How does obsidian-export parse markdown in postprocessors?#^spawn-task-0d2c20]]

# 1 Journal

2025-09-09 Wk 37 Tue - 15:25 +03:00

You can find the library [here](https://github.com/pulldown-cmark/pulldown-cmark/).

Let's build on the project we created in [[001 Parse through all notes and classify as core - peripheral - partial]]

```sh
# in /home/lan/src/cloned/gh/LanHikari22/lan-exp-scripts/scripts/2025/weekly/Wk37-000-obsidian-vault-migration-rs/migration_rs
cargo add pulldown-cmark
```

2025-09-09 Wk 37 Tue - 15:48 +03:00

Checked `tutorials/2025/topics/db/diesel.rs/getting-started/diesel_demo/src/bin/get_post.rs` for basic cli argument parsing

Checked `~/src/cloned/gh/LanHikari22/lan_rs_common/src/util/io.rs` for file IO

2025-09-09 Wk 37 Tue - 16:04 +03:00

We can now run

```sh
# in /home/lan/src/cloned/gh/LanHikari22/lan-exp-scripts/scripts/2025/weekly/Wk37-000-obsidian-vault-migration-rs/migration_rs
cargo run --bin ex000_parse_single_pulldown_cmark ~/src/cloned/gh/LanHikari22/lan-setup-notes/README.md
```

and get all the pulldown events printed for that speciifc markdown file.

We will see headings like this, which are of interest for [[001 Parse through all notes and classify as core - peripheral - partial|our classification task]]:

```
Event Start(Heading { level: H1, id: None, classes: [], attrs: [] })
Event Text(Borrowed("1 What is this?"))
Event End(Heading(H1))
```
