---
deprecates: "[[001 Looking into heading level graph views]]"
breaks: "[[000 Setting up time logging in Obsidian]]"
---

#migration #repo
# 1 Objective

We need to migrate our current obsidian note repos according to the proposals outlined in [001 Sept 8 Obsidian note process change.md](https://github.com/deltatraced/delta-trace/blob/webview/lan/entries/2025/001%20Sept%208%20Obsidian%20note%20process%20change.md)

This would be very time consuming to do manually. We need to automate this. 

Ultimately we should create a migration script that can be run on an obsidian repository to achieve our migration objectives, and then we can privately run it on all the obsidian repos we maintain.

The proposal also outlined automation commands to make using and creating big notes and context spawn trees easy.

# 2 Related

This will be deprecated: [[001 Looking into heading level graph views]] because of the context atomicity principle we will enforce on the note repository.

This introduces breaking changes for [[000 Setting up time logging in Obsidian]]

# 3 Journal

2025-09-17 Wk 38 Wed - 03:44 +03:00

We should have the building blocks necessary for starting the migration.

Spawn [[009 Impl and carry out cluster note migration]] ^spawn-task-3773c5

2025-09-17 Wk 38 Wed - 03:50 +03:00

Spawn [[000 Precommit errors prior to expt004 commit]] ^spawn-issue-7a20bd

2025-09-17 Wk 38 Wed - 12:38 +03:00

Commited to [gh deltachives/2025-Wk37-000-obsidian-migration](https://github.com/deltachives/2025-Wk37-000-obsidian-migration),

```
7f023e8 (HEAD -> main, origin/main) expt004 information extracted like links and spawns
```

# 4 External Links

# 5 References

# 6 Index

## 6.1 Entries

[[000 Break down lan-exp-scripts into an org]]
## 6.2 HowTos

[[000 Include all crate features in build and vscode]]

[[001 Specifying arguments and returns in rust function docs]]

[[002 Copying folder structure but modifying some files in Rust]]

[[003 Rename github origin master branch to main]]
## 6.3 Ideas
## 6.4 Inferences
## 6.5 Investigations

[[000 How should we parse markdown files in Rust?]]

[[001 How does obsidian-export parse markdown in postprocessors?]]

## 6.6 Issues
## 6.7 Tasks

[[000 Create Obsidian extension for actions]]

**todo** [[001 Parse through all notes and classify as core - peripheral - partial]]

[[002 Parse a single obsidian markdown file with pulldown cmark]]

[[003 Make everything under lan_rs_common featured for very minimal includes]]

[[004 Parse parent frontmatter property for time log summaries]]

**todo** [[005 Create Spawn Note Command]]

**todo** [[006 Regenerate cluster core note index heading]]