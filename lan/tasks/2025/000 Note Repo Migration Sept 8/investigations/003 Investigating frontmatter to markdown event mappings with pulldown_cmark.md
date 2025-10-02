---
parent: '[[000 Note Repo Migration Sept 8]]'
spawned_by: '[[001 Parse through all notes and classify as core - peripheral - partial]]'
context_type: investigation
status: done
---

Parent: [000 Note Repo Migration Sept 8](../000%20Note%20Repo%20Migration%20Sept%208.md)

Spawned by: [001 Parse through all notes and classify as core - peripheral - partial](../tasks/001%20Parse%20through%20all%20notes%20and%20classify%20as%20core%20-%20peripheral%20-%20partial.md)

Spawned in: [<a name="spawn-invst-3d5e66" />^spawn-invst-3d5e66](../tasks/001%20Parse%20through%20all%20notes%20and%20classify%20as%20core%20-%20peripheral%20-%20partial.md#spawn-invst-3d5e66)

# 1 Journal

2025-09-13 Wk 37 Sat - 19:44 +03:00

Looking at expected markdown events for frontmatter by running `expt000`,

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt000_parse_single_pulldown_cmark ~/src/cloned/gh/LanHikari22/lan-setup-notes/lan/tasks/2025/000\ Note\ Repo\ Migration\ Sept\ 8/entries/000\ Break\ down\ lan-exp-scripts\ into\ an\ org.md | less
````

The frontmatter:

````
---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[001 Parse through all notes and classify as core - peripheral - partial]]"
context_type: entry
---
````

The events:

````
Event Start(Heading { level: H2, id: None, classes: [], attrs: [] })
Event Text(Boxed("parent: \"[[000 Note Repo Migration Sept 8]]\""))
Event SoftBreak
Event Text(Boxed("spawned_by: \"[[001 Parse through all notes and classify as core - peripheral - partial]]\""))
Event SoftBreak
Event Text(Borrowed("context_type: entry"))
Event End(Heading(H2))
````

There's also a single event at the beginning [`Event Rule`](https://docs.rs/pulldown-cmark/latest/pulldown_cmark/enum.Event.html#variant.Rule). This matches the `---`, though it does not include a second rule even though we end with `---`.

This seems consistent across different files.

So we expect `Rule H2 (Text [Softbreak] ...) /H2` to parse the frontmatter section, and it needs to be at the beginning of the file.
