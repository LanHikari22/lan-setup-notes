---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[000 How should we parse markdown files in Rust?]]"
context_type: investigation
status: done
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[000 How should we parse markdown files in Rust?]]

Spawned in: [[000 How should we parse markdown files in Rust?#^spawn-invst-4917e8|^spawn-invst-4917e8]]

# 1 Journal

2025-09-09 Wk 37 Tue - 15:01 +03:00

The project is [gh zoni/obsidian-export](https://github.com/zoni/obsidian-export)

In their [Cargo.toml](https://github.com/zoni/obsidian-export/blob/main/Cargo.toml) we find that they use [gh pulldown-cmark/pulldown-cmark](https://github.com/pulldown-cmark/pulldown-cmark/).

2025-09-09 Wk 37 Tue - 15:13 +03:00

They have an explanation there about pull parsing. There's a [paper](https://www.xmlpull.org/history/index.html) (referenced from [xmlpull.org](https://www.xmlpull.org/)) explaining this.

They explain:

> With pull API the application is in control and requires next XML event from the parser when it is ready to process it. That means that structure of code that is doing parsing _reflects_ structure of XML documents. This is fundamental pattern visible in all applications using pull based APIs and leads to easier to understand code.

2025-09-09 Wk 37 Tue - 15:20 +03:00

Spawn [[002 Parse a single obsidian markdown file with pulldown cmark]] ^spawn-task-0d2c20
