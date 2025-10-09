---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[013 Change all delta-trace old format notes into note clusters without applying link fixing]]"
context_type: judgment
status: done
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[013 Change all delta-trace old format notes into note clusters without applying link fixing]]

Spawned in: [[013 Change all delta-trace old format notes into note clusters without applying link fixing#^spawn-jdgmt-4ce159|^spawn-jdgmt-4ce159]]

# 1 Judgment


Function `fn get_note_old_format_entries` requires events to do the extraction of old format entries, and yet this cannot be done as it [[001 get_note_old_format_entries requires events but cannot be rendered back trivially due to adhoc patching|(1) requires us to know the original text window of the split]]. Changes that [[002 Cmark to Obsidian Writeback OK Should allow some editing with pulldown cmark|(2) do not require file-splitting]] may still be permissible to be done via events.

$\therefore$  We need to implement `fn get_note_old_format_entries_from_content` to get the boundaries as text blobs and do all the moving before touching any events.

# 2 Reasons

(1)

2025-10-06 Wk 41 Mon - 11:52 +03:00

Spawn [[001 get_note_old_format_entries requires events but cannot be rendered back trivially due to adhoc patching]] ^spawn-infer-468ab5

(2)

2025-10-06 Wk 41 Mon - 11:57 +03:00

Spawn [[002 Cmark to Obsidian Writeback OK Should allow some editing with pulldown cmark]] ^spawn-infer-528f7b

# 3 Journal


