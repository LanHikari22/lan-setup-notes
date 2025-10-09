---
parent: '[[000 Note Repo Migration Sept 8]]'
spawned_by: '[[001 Parse through all notes and classify as core - peripheral - partial]]'
context_type: investigation
status: done
---

Parent: [000 Note Repo Migration Sept 8](../000%20Note%20Repo%20Migration%20Sept%208.md)

Spawned by: [001 Parse through all notes and classify as core - peripheral - partial](../tasks/001%20Parse%20through%20all%20notes%20and%20classify%20as%20core%20-%20peripheral%20-%20partial.md)

Spawned in: [<a name="spawn-invst-c00179" />^spawn-invst-c00179](../tasks/001%20Parse%20through%20all%20notes%20and%20classify%20as%20core%20-%20peripheral%20-%20partial.md#spawn-invst-c00179)

# 1 Journal

2025-09-13 Wk 37 Sat - 16:23 +03:00

I'm trying

````rust
let dir = folder.read_dir().map_err(|e| e.to_string())?;
````

for some `PathBuf` folder.

And apparently you can get its entries?

````rust
let dir_entries = dir
	.collect::<Result<Vec<_>, _>>()
	.map_err(|e| e.to_string())?;
````

Let's create an example binary `ex002` for this.

Checked `/home/lan/src/cloned/gh/deltachives/2025-001-tut-diesel-rs/src/bin/get_post.rs` for basic argparsing in rust

2025-09-13 Wk 37 Sat - 16:35 +03:00

````rust
use std::env::args;
use std::path::PathBuf;

fn main() {
    let folder = args()
        .nth(1)
        .expect("Please provide a folder path")
        .parse::<PathBuf>()
        .expect("Failed to parse folder as a path");

    let dir = folder.read_dir().unwrap();

    let dir_entries = dir.collect::<Result<Vec<_>, _>>().unwrap();

    println!("{dir_entries:?}");
}
````

Let's try to run it

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin ex002_dir_entries "/home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration"

# out (relevant)
[DirEntry("/home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration/.gitignore"), DirEntry("/home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration/target"), DirEntry("/home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration/README.md"), DirEntry("/home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration/.git"), DirEntry("/home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration/src"), DirEntry("/home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration/Cargo.toml"), DirEntry("/home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration/Cargo.lock")]
````

So directories are also entries.

2025-09-13 Wk 37 Sat - 16:40 +03:00

````rust
use std::env::args;
use std::path::PathBuf;

fn main() {
    let folder = args()
        .nth(1)
        .expect("Please provide a folder path")
        .parse::<PathBuf>()
        .expect("Failed to parse folder as a path");

    let dir = folder.read_dir().unwrap();

    let dir_entries = dir.collect::<Result<Vec<_>, _>>().unwrap();

    let folder_entries = dir_entries
        .iter()
        .filter(|entry| entry.metadata().unwrap().is_dir())
        .collect::<Vec<_>>();

    let file_entries = dir_entries
        .iter()
        .filter(|entry| entry.metadata().unwrap().is_file())
        .collect::<Vec<_>>();

    println!("{folder_entries:?}\n");
    println!("{file_entries:?}");
}
````

Entry metadata shows it can be a file, directory, or symlink.

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin ex002_dir_entries "/home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration"

# out (relevant)
[DirEntry("/home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration/target"), DirEntry("/home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration/.git"), DirEntry("/home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration/src")]

[DirEntry("/home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration/.gitignore"), DirEntry("/home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration/README.md"), DirEntry("/home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration/Cargo.toml"), DirEntry("/home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration/Cargo.lock")]
````
