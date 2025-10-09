---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[005 Create Spawn Note Command]]"
context_type: task
status: todo
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[005 Create Spawn Note Command]]

Spawned in: [[005 Create Spawn Note Command#^spawn-task-d5eb50|^spawn-task-d5eb50]]

# 1 Journal

2025-10-07 Wk 41 Tue - 10:31 +03:00

We want to generate both an `Index` H1 heading which just lists the peripheral notes grouped by type, and also a `Spawn Trees` H1 heading that uses bullet points to visually indicate tree level. It's plural `Trees` because there are possibly multiple roots, 

Spawns may come from outside the cluster. For the spawn trees, we want to go over all peripheral notes and ask for their spawner, and then using commonality, we build trees.

2025-10-07 Wk 41 Tue - 10:49 +03:00

```ts
// in main.ts
import * as update_cluster_index_command from "./update_cluster_index_command";

// in main.ts
// in export default class LanPlugin extends Plugin
// in fn async onload()
this.addCommand({
	id: "update-cluster-index",
	name: "Update Cluster Index",
	editorCallback: update_cluster_index_command.run,
});
```

```ts
// in update_cluster_index_command.ts
import {
	Editor,
	MarkdownView,
} from "obsidian";

export async function run(editor: Editor, view: MarkdownView) {
    throw new Error("unimplemented");
}
```

2025-10-07 Wk 41 Tue - 11:20 +03:00

To implement `fn delete_index_and_spawn_trees_headings`, We can process sequentially until we get an H1 heading with `Index` or `Spawn Trees`.

then we keep deleting, until we encounter a different H1 heading.

2025-10-07 Wk 41 Tue - 12:02 +03:00

It seems unlikely that a core note will have `# Index` or `# Spawn Trees`  in codeblocks, so no need to handle that. If this causes issues, it will likely be visible to the user immediately. 

2025-10-07 Wk 41 Tue - 12:26 +03:00

The command to generate index and spawn trees is implemented at the high level, all that remains is to compute the index and the spawn trees themselves.