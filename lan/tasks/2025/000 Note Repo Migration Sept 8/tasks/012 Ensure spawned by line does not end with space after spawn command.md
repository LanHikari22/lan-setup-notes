---
parent: '[[000 Note Repo Migration Sept 8]]'
spawned_by: '[[011 Create integration tests for obsidian patch fixes for sept 8 migration]]'
context_type: task
status: done
---

Parent: [000 Note Repo Migration Sept 8](../000%20Note%20Repo%20Migration%20Sept%208.md)

Spawned by: [011 Create integration tests for obsidian patch fixes for sept 8 migration](011%20Create%20integration%20tests%20for%20obsidian%20patch%20fixes%20for%20sept%208%20migration.md)

Spawned in: [<a name="spawn-task-99507e" />^spawn-task-99507e](011%20Create%20integration%20tests%20for%20obsidian%20patch%20fixes%20for%20sept%208%20migration.md#spawn-task-99507e)

# 1 Related

[000 Create Obsidian extension for actions](000%20Create%20Obsidian%20extension%20for%20actions.md)

# 2 Journal

2025-09-30 Wk 40 Tue - 08:24 +03:00

````sh
# in /home/lan/src/cloned/gh/LanHikari22/lan-obsidian-plugin
git commit -m "update spec and rename bignote to note cluster"

# out
[main 303888d] update spec and rename bignote to note cluster
 Date: Tue Sep 30 08:26:06 2025 +0300
 5 files changed, 75 insertions(+), 60 deletions(-)
 rename src/{bignote.ts => notecluster.ts} (83%)
 rename src/{spawn_note_command.ts => spawn_peripheral_note_command.ts} (90%)
````

2025-09-30 Wk 40 Tue - 08:30 +03:00

````diff
# in spawn_peripheral_note_command.ts
# in function run_with_user_input
-`Spawned by: [[${spawner_file.basename}]] \n\n` +
+`Spawned by: [[${spawner_file.basename}]]\n\n` +
````

2025-09-30 Wk 40 Tue - 08:34 +03:00

````sh
# in /home/lan/src/cloned/gh/LanHikari22/lan-obsidian-plugin
git commit -m "remove unnecessary space at end of Spawned by"

# out
[main da9cbfd] remove unnecessary space at end of Spawned by
 1 file changed, 1 insertion(+), 1 deletion(-)
````

Now let's test it.

2025-09-30 Wk 40 Tue - 08:38 +03:00

````
npm run build
````

It should be working immediately for this vault since we temporarily have a local soft link for testing.

2025-09-30 Wk 40 Tue - 08:43 +03:00

Just have to make sure to restart obsidian also. We're no longer getting those spaces at the end of the line, so we're good!
