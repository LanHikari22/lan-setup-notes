---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[000 Note Repo Migration Sept 8]]"
context_type: task
status: todo
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[000 Note Repo Migration Sept 8]] 

Spawned in: [[000 Note Repo Migration Sept 8#^spawn-task-3773c5|^spawn-task-3773c5]]

# 1 Journal

2025-09-17 Wk 38 Wed - 03:50 +03:00

2025-09-17 Wk 38 Wed - 21:23 +03:00

We're going to start by testing this on delta-trace. We write notes here for the migration, so modifying while capturing is awkward.

2025-09-17 Wk 38 Wed - 21:29 +03:00

Checked [gh LanHikari22/bn_repo_editor](https://github.com/LanHikari22/bn_repo_editor) for clap use for argparsing with [docs.rs clap](https://docs.rs/clap/latest/clap/).

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo add clap --features "cargo"
```

Spawn [[004 Writing events back to file with pulldown cmark]] ^spawn-howto-6ee267

2025-09-23 Wk 39 Tue - 04:16 +03:00

So we're able to convert from events back to markdown files, but this will likely introduce some minor noise, like line trims or spacing. Let's add a subcommand to just read events and write them back for all markdown files in the vault.

2025-09-23 Wk 39 Tue - 04:55 +03:00

Had to redo some minor work for some reason, but everything is commited now.

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin app -- writeback ~/src/cloned/gh/deltatraced/delta-trace

# out (error, relevant)
thread 'main' panicked at /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration/src/drivers.rs:67:10:
Failed to get working items
```

2025-09-23 Wk 39 Tue - 05:34 +03:00

Changed some `Option<T>` opaque errors into detailed errors per function.

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin app -- writeback ~/src/cloned/gh/deltatraced/delta-trace

# out (error, relevant)
thread 'main' panicked at /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration/src/drivers.rs:67:10:
Failed to get working items: FailedToGetCategoryFoldersAndPeripherals(NotAClusterCategoryFolderPath("/home/lan/src/cloned/gh/deltatraced/delta-trace/lan/templates/000 big note/side-notes"))
```

Oh I see! I still had that old template folder there when we started designing cluster notes. Let's delete it.

2025-09-23 Wk 39 Tue - 05:41 +03:00

Spawn [[010 Apply markdown writeback on delta-trace vault]] ^spawn-task-e5af29

2025-09-30 Wk 40 Tue - 08:40 +03:00

Spawn [[013 Change all delta-trace old format notes into note clusters without applying link fixing]] ^spawn-task-479e16
