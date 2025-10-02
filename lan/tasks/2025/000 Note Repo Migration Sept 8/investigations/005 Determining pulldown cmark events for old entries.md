---
parent: '[[000 Note Repo Migration Sept 8]]'
spawned_by: '[[001 Parse through all notes and classify as core - peripheral - partial]]'
context_type: investigation
status: done
---

Parent: [000 Note Repo Migration Sept 8](../000%20Note%20Repo%20Migration%20Sept%208.md)

Spawned by: [001 Parse through all notes and classify as core - peripheral - partial](../tasks/001%20Parse%20through%20all%20notes%20and%20classify%20as%20core%20-%20peripheral%20-%20partial.md)

Spawned in: [<a name="spawn-invst-7d671c" />^spawn-invst-7d671c](../tasks/001%20Parse%20through%20all%20notes%20and%20classify%20as%20core%20-%20peripheral%20-%20partial.md#spawn-invst-7d671c)

# 1 Journal

2025-09-14 Wk 37 Sun - 03:47 +03:00

2025-09-14 Wk 37 Sun - 10:37 +03:00

There's complexity to be considered when extracting portions of files into their own files.

We need to track all linkable block identifiers in them and headings, and if those are referenced anywhere, they need to be updated against the new note spawned.

We need to preserve already existing spawn lineages and replace the "Spawn" lines with new spawned item links. Note that early on, I was inconsistent with how to do spawns, so don't expect a constant format. It should be consistent to find spawn block identifiers `^spawn-{cat}-{randhex6}` Call that `{spawn_block_identifier}`.

If we find lines like `Spawn {note} {spawn_block_identifier}`, then they need to be updated with the new note link.

2025-09-14 Wk 37 Sun - 13:04 +03:00

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt000_parse_single_pulldown_cmark ~/src/cloned/gh/LanHikari22/lan-setup-notes/lan/topics/tooling/mobile/entries/2025/000\ Install\ open\ source\ mobile\ OS\ on\ my\ phone.md  | less
````

This has 3 tasks, 1 side note. The tasks reference the others in multiple places.

There are also no Spawns. Notice that spawning itself was introduced later as a concept and practice.

We also have autonumbered headers, and the autonumbering needs to be dismissed.

````
Event Start(Heading { level: H1, id: None, classes: [], attrs: [] })
Event Text(Borrowed("3 Tasks"))
Event End(Heading(H1))
````

Once we find an H1 of interest marked like this, we can start grabbing all the events of H2s within it.

````
Event Start(Heading { level: H2, id: None, classes: [], attrs: [] })
Event Text(Borrowed("3.1 Install GrapheneOS on my Pixel 3"))
Event End(Heading(H2))
````

````
Event Start(Heading { level: H2, id: None, classes: [], attrs: [] })
Event Text(Borrowed("3.2 Install LineageOS on my Pixel 3"))
Event End(Heading(H2))
````

All events between those two are the content for the first task. The last item will go either to end of events or to another H2 or H1.

We should start by parsing all the content of those records, and all defined block identifiers and headings in their content. Those are the two ways anything in that segment may be linked.
