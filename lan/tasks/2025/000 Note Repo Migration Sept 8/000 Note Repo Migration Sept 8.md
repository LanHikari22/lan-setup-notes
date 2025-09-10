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

# 4 External Links

# 5 References

# 6 Index

## 6.1 Entries

[[000 Break down lan-exp-scripts into an org]]
## 6.2 HowTos

[[000 Include all crate features in build and vscode]]

[[001 Specifying arguments and returns in rust function docs]]

[[002 Copying folder structure but modifying some files in Rust]]
## 6.3 Ideas
## 6.4 Inferences
## 6.5 Investigations

[[000 How should we parse markdown files in Rust?]]

[[001 How does obsidian-export parse markdown in postprocessors?]]

## 6.6 Issues
## 6.7 Tasks

**Todo** [[000 Create Obsidian extension for actions]]

**Todo** [[001 Parse through all notes and classify as big small or hybrid note]]

[[002 Parse a single obsidian markdown file with pulldown cmark]]

[[003 Make everything under lan_rs_common featured for very minimal includes]]

[[004 Parse parent frontmatter property for time log summaries]]

**Todo** [[005 Create Spawn Note Command]]
