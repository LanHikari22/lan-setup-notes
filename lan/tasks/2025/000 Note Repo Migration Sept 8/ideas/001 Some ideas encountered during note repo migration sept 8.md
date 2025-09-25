---
parent: '[[000 Note Repo Migration Sept 8]]'
spawned_by: '[[000 Note Repo Migration Sept 8]]'
context_type: idea
---

Parent: [000 Note Repo Migration Sept 8](../000%20Note%20Repo%20Migration%20Sept%208.md)

Spawned by: [000 Note Repo Migration Sept 8](../000%20Note%20Repo%20Migration%20Sept%208.md)

Spawned in: [<a name="spawn-idea-57ecd9" />^spawn-idea-57ecd9](../000%20Note%20Repo%20Migration%20Sept%208.md#spawn-idea-57ecd9)

# 1 Journal

(1)

2025-09-23 Wk 39 Tue - 00:01 +03:00

Since every peripheral note now has a random 6 hex digit identifier via spawns, it could be possible to always identify them. For example, if they're arbitrarily renamed or relocated in the future but the id remains in tact, we could search version control for that id and identify their old name and location for link fixing.

Although location should not be a big issue, since we refer to them by their filename, but if they are being referred to from the outside by another vault it might be an issue. I do think we should eventually look into working with networked notes, in a similar fashion. For example, `[[NNN my networked note#with some heading|and some title@vault-url]]`

Although to support getting all notes from a networked vault, maybe `[[vault-url:NNN my networked note#with some heading|and some title]]`
