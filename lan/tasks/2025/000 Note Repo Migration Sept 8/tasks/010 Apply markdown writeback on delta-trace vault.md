---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[009 Impl and carry out cluster note migration]]"
context_type: task
status: todo
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[009 Impl and carry out cluster note migration]] 

Spawned in: [[009 Impl and carry out cluster note migration#^spawn-task-e5af29|^spawn-task-e5af29]]

# 1 Journal

2025-09-23 Wk 39 Tue - 05:41 +03:00

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin app -- writeback ~/src/cloned/gh/deltatraced/delta-trace

# in /home/lan/src/cloned/gh/deltatraced/delta-trace
git status | wc -l

# out
240
```

Lots of things changed.

First issue encountered:

Spawn [[001 pulldown cmark to cmark escapes first obsidian tag on writeback]] ^spawn-issue-9b47f5
