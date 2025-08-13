---
spawned: "[[002 Create an experimental obsidian extension to test ideas in]]"
status: todo
---

# 1 Objective


My note repositories are interconnected. I have all of them local on my machine, but when I want to reference something from another repostiory, now I have to commit everything on both, sync them, see both pages in github, copy those links, and reference them, and ensure there's a backlink in the other repository too for backtracking logs.

That's a lot of effort. So given the assumptions that

1. This is managed by some known supported service (for me, github.com)
2. URLs like for Github.com are deterministic and known based on the folder structure and filename
3. Links are likely being followed and viewed within that service

We should make an extension that makes this seamless and without need to commit content yet. This may result in interim broken links until the next commit syncing of the other repositories, but this should be fine.
