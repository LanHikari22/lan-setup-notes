---
parent: '[[000 Note Repo Migration Sept 8]]'
spawned_by: '[[001 Parse through all notes and classify as core - peripheral - partial]]'
context_type: task
status: done
resolved: partial
---

Parent: [000 Note Repo Migration Sept 8](../000%20Note%20Repo%20Migration%20Sept%208.md)

Spawned by: [001 Parse through all notes and classify as core - peripheral - partial](001%20Parse%20through%20all%20notes%20and%20classify%20as%20core%20-%20peripheral%20-%20partial.md)

Spawned in: [<a name="spawn-task-22c0e7" />^spawn-task-22c0e7](001%20Parse%20through%20all%20notes%20and%20classify%20as%20core%20-%20peripheral%20-%20partial.md#spawn-task-22c0e7)

# 1 Journal

2025-09-15 Wk 38 Mon - 20:35 +03:00

Let's start with `001 Turn EWRAM and ROM Structs into C Structs and embed into type for gdb memory manipulation` in dism-exe-notes. This is one of the biggest files, and also earliest, so the notation varied wildly there.

2025-09-16 Wk 38 Tue - 06:01 +03:00

Some thing we wrote and moved here from a comment:

````
    // Our latest Spawned format is
    // (0) "From {block_identifier} in {spawner_note}"
    // where {block_identifier} breaks into "spawn-{category}-{randhexstr6}"

    // But we also used others before it
    // (1) "From {spawner_note}."
    // (2) "From {block_identifier}."

    // Some are actually broken. For example, we manually would add "spawn-{category}-" to the 6-digit random hex
    // identifier created by obsidian. But then we would forget to add it there. But you should still be able to infer this.
    // (3) "From {broken_block_identifier}" in {spawner_note}.
    // Of course, Anticipate {broken_block_identifier} in (2) too.

    // Similarly for Spawn, the format is
    // (0) Spawn {spawned_note} {block_identifier}

    // But before that we would add titles, or even just put it in the same line as other text.

    // It might be best to assume latest format and fix the rest manually.
````
