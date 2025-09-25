---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[001 Parse through all notes and classify as core - peripheral - partial]]"
context_type: investigation
status: done
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[001 Parse through all notes and classify as core - peripheral - partial]]

Spawned in: [[001 Parse through all notes and classify as core - peripheral - partial#^spawn-invst-38f502|^spawn-invst-38f502]]

# 1 Objective

We need to determine the method we will use to parse markdown files in our rust project.

# 2 Journal

2025-09-09 Wk 37 Tue - 14:53 +03:00

We could do something similar to [obsidian-export](https://github.com/zoni/obsidian-export). 

Spawn [[001 How does obsidian-export parse markdown in postprocessors?]] ^spawn-invst-4917e8

2025-09-09 Wk 37 Tue - 15:22 +03:00

Let's try pulldown cmark over obsidian notes and see what we get.

2025-09-09 Wk 37 Tue - 16:06 +03:00

This looks good. Especially since right now what we're interested in is just parsing headings. We will though have to worry about obsidian links.