---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[001 pulldown cmark to cmark escapes first obsidian tag on writeback]]"
context_type: issue
status: done
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[001 pulldown cmark to cmark escapes first obsidian tag on writeback]] 

Spawned in: [[001 pulldown cmark to cmark escapes first obsidian tag on writeback#^spawn-issue-07b5a7|^spawn-issue-07b5a7]]

# 1 Journal


2025-09-24 Wk 39 Wed - 03:26 +03:00

It just says the file isn't included anywhere, so it won't analyze it. 

[gh rust-lang/rust-analyzer #2034](https://github.com/rust-lang/rust-analyzer/issues/2034)

[post rust-lang](https://users.rust-lang.org/t/vscode-rust-analyzer-doesnt-function-in-tests-folder/94790)

2025-09-24 Wk 39 Wed - 03:30 +03:00

Okay the issue for me was just that I accidentally had `tests/` inside `src/` when it should be outside since integration tests are not part of the crate.