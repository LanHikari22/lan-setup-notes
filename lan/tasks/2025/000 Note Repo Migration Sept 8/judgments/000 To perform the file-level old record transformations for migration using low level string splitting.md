---
parent: '[[000 Note Repo Migration Sept 8]]'
spawned_by: '[[013 Change all delta-trace old format notes into note clusters without applying link fixing]]'
context_type: judgment
status: done
---

Parent: [000 Note Repo Migration Sept 8](../000%20Note%20Repo%20Migration%20Sept%208.md)

Spawned by: [013 Change all delta-trace old format notes into note clusters without applying link fixing](../tasks/013%20Change%20all%20delta-trace%20old%20format%20notes%20into%20note%20clusters%20without%20applying%20link%20fixing.md)

Spawned in: [<a name="spawn-jdgmt-4ce159" />^spawn-jdgmt-4ce159](../tasks/013%20Change%20all%20delta-trace%20old%20format%20notes%20into%20note%20clusters%20without%20applying%20link%20fixing.md#spawn-jdgmt-4ce159)

# 1 Judgment

Function `fn get_note_old_format_entries` requires events to do the extraction of old format entries, and yet this cannot be done as it [(1) requires us to know the original text window of the split](../inferences/001%20get_note_old_format_entries%20requires%20events%20but%20cannot%20be%20rendered%20back%20trivially%20due%20to%20adhoc%20patching.md). Changes that [(2) do not require file-splitting](../inferences/002%20Cmark%20to%20Obsidian%20Writeback%20OK%20Should%20allow%20some%20editing%20with%20pulldown%20cmark.md) may still be permissible to be done via events.

$\therefore$  We need to implement `fn get_note_old_format_entries_from_content` to get the boundaries as text blobs and do all the moving before touching any events.

# 2 Reasons

(1)

2025-10-06 Wk 41 Mon - 11:52 +03:00

Spawn [001 get_note_old_format_entries requires events but cannot be rendered back trivially due to adhoc patching](../inferences/001%20get_note_old_format_entries%20requires%20events%20but%20cannot%20be%20rendered%20back%20trivially%20due%20to%20adhoc%20patching.md) <a name="spawn-infer-468ab5" />^spawn-infer-468ab5

(2)

2025-10-06 Wk 41 Mon - 11:57 +03:00

Spawn [002 Cmark to Obsidian Writeback OK Should allow some editing with pulldown cmark](../inferences/002%20Cmark%20to%20Obsidian%20Writeback%20OK%20Should%20allow%20some%20editing%20with%20pulldown%20cmark.md) <a name="spawn-infer-528f7b" />^spawn-infer-528f7b

# 3 Journal
