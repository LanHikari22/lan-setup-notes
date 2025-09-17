---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[001 Parse through all notes and classify as core - peripheral - partial]]"
context_type: entry
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned in [[001 Parse through all notes and classify as core - peripheral - partial#^spawn-entry-e65ac0]]

# 1 Journal

2025-09-09 Wk 37 Tue - 16:25 +03:00

Our work right now is all about respecting file atomicity of context. By this, I mean that each file has exactly one context, and it links or indexes other contexts otherwise. 

We created [lan-exp-scripts](https://github.com/LanHikari22/lan-exp-scripts) with the hope to provide public storage for all scripts and files associated with our notes, but it violates the atomicity principle. This repository is an "everything repository", which makes it not very usable, blends all git histories of everything we ever do together, and hinders usability.

The design decision behind this was because it was visualized to just be a place to put all scripts in without overrunning our repository count with many little script repositories. For this, we will instead create a dedicated org, `deltalab` where we can create as many experimental and small repositories as we want. This can also keep `deltatraced` from getting diluted with many repositories.

We followed a methodology of weekly streams where we classify the project by week. We can still do this. Even tutorial reproductions end up in `lan-exp-scripts`, as well as templates.

2025-09-09 Wk 37 Tue - 16:49 +03:00

Ok! `deltalab` was taken, but we created [deltachives](https://github.com/deltachives)!

2025-09-09 Wk 37 Tue - 17:04 +03:00

With this, we restore the practice all git repositories we have are very focused and have a single purpose, rather than be treated as a catch-all archive. Let an org do that!