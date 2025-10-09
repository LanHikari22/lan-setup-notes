---
parent: '[[000 Note Repo Migration Sept 8]]'
spawned_by: '[[000 How should we parse markdown files in Rust?]]'
context_type: investigation
status: done
---

Parent: [000 Note Repo Migration Sept 8](../000%20Note%20Repo%20Migration%20Sept%208.md)

Spawned by: [000 How should we parse markdown files in Rust?](000%20How%20should%20we%20parse%20markdown%20files%20in%20Rust%3F.md)

Spawned in: [<a name="spawn-invst-4917e8" />^spawn-invst-4917e8](000%20How%20should%20we%20parse%20markdown%20files%20in%20Rust%3F.md#spawn-invst-4917e8)

# 1 Journal

2025-09-09 Wk 37 Tue - 15:01 +03:00

The project is [gh zoni/obsidian-export](https://github.com/zoni/obsidian-export)

In their [Cargo.toml](https://github.com/zoni/obsidian-export/blob/main/Cargo.toml) we find that they use [gh pulldown-cmark/pulldown-cmark](https://github.com/pulldown-cmark/pulldown-cmark/).

2025-09-09 Wk 37 Tue - 15:13 +03:00

They have an explanation there about pull parsing. There's a [paper](https://www.xmlpull.org/history/index.html) (referenced from [xmlpull.org](https://www.xmlpull.org/)) explaining this.

They explain:

 > 
 > With pull API the application is in control and requires next XML event from the parser when it is ready to process it. That means that structure of code that is doing parsing *reflects* structure of XML documents. This is fundamental pattern visible in all applications using pull based APIs and leads to easier to understand code.

2025-09-09 Wk 37 Tue - 15:20 +03:00

Spawn [002 Parse a single obsidian markdown file with pulldown cmark](../tasks/002%20Parse%20a%20single%20obsidian%20markdown%20file%20with%20pulldown%20cmark.md) <a name="spawn-task-0d2c20" />^spawn-task-0d2c20
