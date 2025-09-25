---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[001 Parse through all notes and classify as core - peripheral - partial]]"
context_type: howto
status: done
resolved: partial
---
Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[001 Parse through all notes and classify as core - peripheral - partial]]

Spawned in: [[001 Parse through all notes and classify as core - peripheral - partial#^spawn-howto-9a69d0|^spawn-howto-9a69d0]]

# 1 Objective

Instead of globbing, we should consider taking a repository and outputting a repository for our migration. No in-place modifications. For many files, like `.obsidian/*` or `.git/*`, we only want to PASS so they end up simply being copied. For markdown files of interest, we may want to change them. A file may turn into a folder at current directory, and a folder at the current directory may turn into a file.

# 2 Journal

2025-09-10 Wk 37 Wed - 01:23 +03:00

This [blog post](https://nick.groenen.me/notes/recursively-copy-files-in-rust/) and [stackoverflow answer](https://stackoverflow.com/a/65192210/6944447) point to a similar example:

```rust
/// Copy files from source to destination recursively.
pub fn copy_recursively(source: impl AsRef<Path>, destination: impl AsRef<Path>) -> io::Result<()> {
    fs::create_dir_all(&destination)?;
    for entry in fs::read_dir(source)? {
        let entry = entry?;
        let filetype = entry.file_type()?;
        if filetype.is_dir() {
            copy_recursively(entry.path(), destination.as_ref().join(entry.file_name()))?;
        } else {
            fs::copy(entry.path(), destination.as_ref().join(entry.file_name()))?;
        }
    }
    Ok(())
}
```

But this doesn't resolve our issue alone. 

For our use case,

We might be able to use this to copy everything recursively and then modify selective files in the new directory.

We can then delete files, and replace them with folders, or scan for folders of an expected structure and replace them individually with system operations.