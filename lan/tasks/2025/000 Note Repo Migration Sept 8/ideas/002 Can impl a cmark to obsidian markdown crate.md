---
parent: '[[000 Note Repo Migration Sept 8]]'
spawned_by: '[[001 pulldown cmark to cmark escapes first obsidian tag on writeback]]'
context_type: idea
---

Parent: [000 Note Repo Migration Sept 8](../000%20Note%20Repo%20Migration%20Sept%208.md)

Spawned by: [001 pulldown cmark to cmark escapes first obsidian tag on writeback](../issues/001%20pulldown%20cmark%20to%20cmark%20escapes%20first%20obsidian%20tag%20on%20writeback.md)

Spawned in: [<a name="spawn-idea-51f0eb" />^spawn-idea-51f0eb](../issues/001%20pulldown%20cmark%20to%20cmark%20escapes%20first%20obsidian%20tag%20on%20writeback.md#spawn-idea-51f0eb)

# 1 Journal

2025-09-23 Wk 39 Tue - 09:28 +03:00

We're doing a comparison patch in an ad-hoc way, but it's possible to dig further into this for a proper treatment of obsidian markdown.

Also, some problems start in treating obsidian markdown as common markdown in [gh pulldown-cmark/pulldown-cmark](https://github.com/pulldown-cmark/pulldown-cmark/).

frontmatter is not supported, neither are obsidian links.  Those issues are propagated through [gh Byron/pulldown-cmark-to-cmark](https://github.com/Byron/pulldown-cmark-to-cmark) when trying to render back to obsidian markdown.

The spawner context for this idea covers some of the differences and ad-hoc patches attempted.
