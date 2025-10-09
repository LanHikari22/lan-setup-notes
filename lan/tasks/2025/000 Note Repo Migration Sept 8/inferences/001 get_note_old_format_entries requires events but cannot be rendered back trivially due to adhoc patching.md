---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[000 To perform the file-level old record transformations for migration using low level string splitting]]"
context_type: inference
status: done
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[000 To perform the file-level old record transformations for migration using low level string splitting]]

Spawned in: [[000 To perform the file-level old record transformations for migration using low level string splitting#^spawn-infer-468ab5|^spawn-infer-468ab5]]

# 1 Journal

2025-10-06 Wk 41 Mon - 12:19 +03:00

The funciton `fn get_note_old_format_entries` requires events as input, but this is for file-level splitting manipulation as explained in [[000 Choosing pulldown_cmark for cluster note migration was costly]], which cannot be done as we lose information about the full file boundary and thus cannot do diff patching.