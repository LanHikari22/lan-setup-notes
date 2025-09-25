---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[000 Note Repo Migration Sept 8]]"
context_type: entry
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[000 Note Repo Migration Sept 8]] 

Spawned in: [[000 Note Repo Migration Sept 8#^spawn-entry-60fe40|^spawn-entry-60fe40]]

# 1 Journal

During investigating [[008 How does obsidian-export export pulldown-cmark back to markdown?]],

(1)

2025-09-22 Wk 39 Mon - 23:19 +03:00

We found out that they use [docs.rs snafu](https://docs.rs/snafu/latest/snafu/) for error handling. Example usage from obsidian-markdown: [1](https://github.com/zoni/obsidian-export/blob/87cb4ceb0ae48b391da4b02b94c6a909b1eb438d/src/lib.rs#L142).

(2)

2025-09-22 Wk 39 Mon - 23:38 +03:00

In [here](https://github.com/zoni/obsidian-export/blob/87cb4ceb0ae48b391da4b02b94c6a909b1eb438d/src/lib.rs#L250), obsidian-export chooses to explicitly use a formatter's `.debug_struct`, `.field`, and `.finish` to specify how to fully display a struct.