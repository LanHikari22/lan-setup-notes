---
deprecates: '[[001 Looking into heading level graph views]]'
breaks: '[[000 Setting up time logging in Obsidian]]'
---

\#migration #repo

# 1 Objective

We need to migrate our current obsidian note repos according to the proposals outlined in [001 Sept 8 Obsidian note process change.md](https://github.com/deltatraced/delta-trace/blob/webview/lan/entries/2025/001%20Sept%208%20Obsidian%20note%20process%20change.md)

This would be very time consuming to do manually. We need to automate this.

Ultimately we should create a migration script that can be run on an obsidian repository to achieve our migration objectives, and then we can privately run it on all the obsidian repos we maintain.

The proposal also outlined automation commands to make using and creating big notes and context spawn trees easy.

# 2 Related

This will be deprecated: [001 Looking into heading level graph views](../../../topics/tooling/obsidian/entries/2025/001%20Looking%20into%20heading%20level%20graph%20views.md) because of the context atomicity principle we will enforce on the note repository.

This introduces breaking changes for [000 Setting up time logging in Obsidian](../../../topics/tooling/obsidian/entries/2025/000%20Setting%20up%20time%20logging%20in%20Obsidian.md)

# 3 Journal

2025-09-17 Wk 38 Wed - 03:44 +03:00

We should have the building blocks necessary for starting the migration.

Spawn [009 Impl and carry out cluster note migration](tasks/009%20Impl%20and%20carry%20out%20cluster%20note%20migration.md) <a name="spawn-task-3773c5" />^spawn-task-3773c5

2025-09-17 Wk 38 Wed - 03:50 +03:00

Spawn [000 Precommit errors prior to expt004 commit](issues/000%20Precommit%20errors%20prior%20to%20expt004%20commit.md) <a name="spawn-issue-7a20bd" />^spawn-issue-7a20bd

2025-09-17 Wk 38 Wed - 12:38 +03:00

Commited to [gh deltachives/2025-Wk37-000-obsidian-migration](https://github.com/deltachives/2025-Wk37-000-obsidian-migration),

````
7f023e8 (HEAD -> main, origin/main) expt004 information extracted like links and spawns
````

2025-09-22 Wk 39 Mon - 23:19 +03:00

Spawn [001 Resources encountered during note repo migration sept 8](entries/001%20Resources%20encountered%20during%20note%20repo%20migration%20sept%208.md) <a name="spawn-entry-60fe40" />^spawn-entry-60fe40

2025-09-23 Wk 39 Tue - 00:00 +03:00

Spawn [001 Some ideas encountered during note repo migration sept 8](ideas/001%20Some%20ideas%20encountered%20during%20note%20repo%20migration%20sept%208.md) <a name="spawn-idea-57ecd9" />^spawn-idea-57ecd9

2025-09-23 Wk 39 Tue - 09:55 +03:00

Turns out if we have a `main.rs`, it's included in the `cargo run --bin` options as the name of the crate. In our case, `migration_rs`. But we created an `app` under bin. Let's remove `main.rs` since we can use `--bin app`.

2025-10-06 Wk 41 Mon - 05:41 +03:00

Spawn [002 Side notes taken during note repo migration](entries/002%20Side%20notes%20taken%20during%20note%20repo%20migration.md) <a name="spawn-entry-92491b" />^spawn-entry-92491b

2025-10-07 Wk 41 Tue - 07:15 +03:00

Spawn [003 Objective reminders noted during sept 8 migration](entries/003%20Objective%20reminders%20noted%20during%20sept%208%20migration.md) <a name="spawn-entry-cddf08" />^spawn-entry-cddf08

# 4 External Links

# 5 References

# 6 Index

## 6.1 Entries

[000 Break down lan-exp-scripts into an org](entries/000%20Break%20down%20lan-exp-scripts%20into%20an%20org.md)

## 6.2 HowTos

[000 Include all crate features in build and vscode](howtos/000%20Include%20all%20crate%20features%20in%20build%20and%20vscode.md)

[001 Specifying arguments and returns in rust function docs](howtos/001%20Specifying%20arguments%20and%20returns%20in%20rust%20function%20docs.md)

[002 Copying folder structure but modifying some files in Rust](howtos/002%20Copying%20folder%20structure%20but%20modifying%20some%20files%20in%20Rust.md)

[003 Rename github origin master branch to main](howtos/003%20Rename%20github%20origin%20master%20branch%20to%20main.md)

## 6.3 Ideas

## 6.4 Inferences

## 6.5 Investigations

[000 How should we parse markdown files in Rust?](investigations/000%20How%20should%20we%20parse%20markdown%20files%20in%20Rust%3F.md)

[001 How does obsidian-export parse markdown in postprocessors?](investigations/001%20How%20does%20obsidian-export%20parse%20markdown%20in%20postprocessors%3F.md)

## 6.6 Issues

## 6.7 Tasks

[000 Create Obsidian extension for actions](tasks/000%20Create%20Obsidian%20extension%20for%20actions.md)

**todo** [001 Parse through all notes and classify as core - peripheral - partial](tasks/001%20Parse%20through%20all%20notes%20and%20classify%20as%20core%20-%20peripheral%20-%20partial.md)

[002 Parse a single obsidian markdown file with pulldown cmark](tasks/002%20Parse%20a%20single%20obsidian%20markdown%20file%20with%20pulldown%20cmark.md)

[003 Make everything under lan_rs_common featured for very minimal includes](tasks/003%20Make%20everything%20under%20lan_rs_common%20featured%20for%20very%20minimal%20includes.md)

[004 Parse parent frontmatter property for time log summaries](tasks/004%20Parse%20parent%20frontmatter%20property%20for%20time%20log%20summaries.md)

**todo** [005 Create Spawn Note Command](tasks/005%20Create%20Spawn%20Note%20Command.md)

**todo** [006 Regenerate cluster core note index heading](tasks/006%20Regenerate%20cluster%20core%20note%20index%20heading.md)
