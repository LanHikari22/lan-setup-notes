---
parent: '[[000 Note Repo Migration Sept 8]]'
spawned_by: '[[009 Impl and carry out cluster note migration]]'
context_type: task
status: done
---

Parent: [000 Note Repo Migration Sept 8](../000%20Note%20Repo%20Migration%20Sept%208.md)

Spawned by: [009 Impl and carry out cluster note migration](009%20Impl%20and%20carry%20out%20cluster%20note%20migration.md)

Spawned in: [<a name="spawn-task-e5af29" />^spawn-task-e5af29](009%20Impl%20and%20carry%20out%20cluster%20note%20migration.md#spawn-task-e5af29)

# 1 Journal

2025-09-23 Wk 39 Tue - 05:41 +03:00

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin app -- writeback ~/src/cloned/gh/deltatraced/delta-trace

# in /home/lan/src/cloned/gh/deltatraced/delta-trace
git status | wc -l

# out
240
````

Lots of things changed.

Spawn [001 pulldown cmark to cmark escapes first obsidian tag on writeback](../issues/001%20pulldown%20cmark%20to%20cmark%20escapes%20first%20obsidian%20tag%20on%20writeback.md) <a name="spawn-issue-9b47f5" />^spawn-issue-9b47f5
