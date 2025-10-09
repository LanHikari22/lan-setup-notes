---
parent: '[[000 Note Repo Migration Sept 8]]'
spawned_by: '[[000 To perform the file-level old record transformations for migration using low level string splitting]]'
context_type: inference
status: done
---

Parent: [000 Note Repo Migration Sept 8](../000%20Note%20Repo%20Migration%20Sept%208.md)

Spawned by: [000 To perform the file-level old record transformations for migration using low level string splitting](../judgments/000%20To%20perform%20the%20file-level%20old%20record%20transformations%20for%20migration%20using%20low%20level%20string%20splitting.md)

Spawned in: [<a name="spawn-infer-468ab5" />^spawn-infer-468ab5](../judgments/000%20To%20perform%20the%20file-level%20old%20record%20transformations%20for%20migration%20using%20low%20level%20string%20splitting.md#spawn-infer-468ab5)

# 1 Journal

2025-10-06 Wk 41 Mon - 12:19 +03:00

The funciton `fn get_note_old_format_entries` requires events as input, but this is for file-level splitting manipulation as explained in [000 Choosing pulldown_cmark for cluster note migration was costly](000%20Choosing%20pulldown_cmark%20for%20cluster%20note%20migration%20was%20costly.md), which cannot be done as we lose information about the full file boundary and thus cannot do diff patching.
