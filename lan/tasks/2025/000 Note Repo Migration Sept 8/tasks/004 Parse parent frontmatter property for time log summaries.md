---
parent: '[[000 Note Repo Migration Sept 8]]'
context_type: task
status: done
---

Parent: [000 Note Repo Migration Sept 8](../000%20Note%20Repo%20Migration%20Sept%208.md)

# 1 Objective

Once we introduced big note folder structures, we no longer get subitems in our summaries, everything becomes a main item.

All time logs within a file that has a `parent` frontmatter property, should be a child of that parent note, even if it is subheadings within a file.

# 2 Journal

2025-09-10 Wk 37 Wed - 05:23 +03:00

We now are able to scan notes for frontmatter with `parent` property and include those as subitems to the superitem of the parent.

One thing we notice is that the timestamps of the simple time logger are not the same timestamp used here, so some items from next day end up in the same day, at least within midnight. Something to look into, but not a current priority.

Noting issue in [000 Setting up time logging in Obsidian](../../../../topics/tooling/obsidian/entries/2025/000%20Setting%20up%20time%20logging%20in%20Obsidian.md).
