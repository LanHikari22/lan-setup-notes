---
parent: '[[000 Note Repo Migration Sept 8]]'
spawned_by: '[[004 Writing events back to file with pulldown cmark]]'
context_type: investigation
status: done
---

Parent: [000 Note Repo Migration Sept 8](../000%20Note%20Repo%20Migration%20Sept%208.md)

Spawned by: [004 Writing events back to file with pulldown cmark](../howtos/004%20Writing%20events%20back%20to%20file%20with%20pulldown%20cmark.md)

Spawned in: [<a name="spawn-invst-ebcfc2" />^spawn-invst-ebcfc2](../howtos/004%20Writing%20events%20back%20to%20file%20with%20pulldown%20cmark.md#spawn-invst-ebcfc2)

# 1 Journal

2025-09-22 Wk 39 Mon - 23:44 +03:00

[run](https://github.com/zoni/obsidian-export/blob/87cb4ceb0ae48b391da4b02b94c6a909b1eb438d/src/lib.rs#L356) $\to$ [export_note](https://github.com/zoni/obsidian-export/blob/87cb4ceb0ae48b391da4b02b94c6a909b1eb438d/src/lib.rs#L418) $\to$ [parse_and_export_obsidian_note](https://github.com/zoni/obsidian-export/blob/87cb4ceb0ae48b391da4b02b94c6a909b1eb438d/src/lib.rs#L441) $\to$ [render_mdevents_to_mdtext](https://github.com/zoni/obsidian-export/blob/87cb4ceb0ae48b391da4b02b94c6a909b1eb438d/src/lib.rs#L830)

This uses [pulldown_cmark_to_cmark](https://docs.rs/pulldown-cmark-to-cmark/latest/pulldown_cmark_to_cmark/) ([lib.rs](https://lib.rs/crates/pulldown-cmark-to-cmark) [gh](https://github.com/byron/pulldown-cmark-to-cmark)).

2025-09-22 Wk 39 Mon - 23:55 +03:00

Some resources are also recorded in [001 Resources encountered during note repo migration sept 8](../entries/001%20Resources%20encountered%20during%20note%20repo%20migration%20sept%208.md)
