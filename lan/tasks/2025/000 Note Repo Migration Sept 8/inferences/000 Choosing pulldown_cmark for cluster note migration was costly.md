---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[013 Change all delta-trace old format notes into note clusters without applying link fixing]]"
context_type: inference
status: done
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[013 Change all delta-trace old format notes into note clusters without applying link fixing]]

Spawned in: [[013 Change all delta-trace old format notes into note clusters without applying link fixing#^spawn-infer-290240|^spawn-infer-290240]]

# 1 Journal

2025-10-06 Wk 41 Mon - 12:02 +03:00

The first difficulty that was unanticipated was in [[010 Apply markdown writeback on delta-trace vault]] where the bulk of work is in [[001 pulldown cmark to cmark escapes first obsidian tag on writeback]].

All of this was to create `fn adhoc_fix_rendered_markdown_output_for_obsidian`. This function processses diffs using the Myers algoirthm in order to accept or reject changes between common markdown and obsidian.

This currently works for the delta-trace vault, and we are able to do writeback with no diff changes in the end.

However, besides the fragility risk, as it is ad-hoc, it also took a lot of time to implement this, and it imposes heavy limitations for how much editing can be done to the markdown file. If we split the file by using events, then we no longer can tell what the original text is for each split, and thus we cannot apply the adhoc patching as it relies on diffing which requires the original context intact.

Our overall task is to identify entries bundled in the same file, and split them up according to our new cluster note format. This requires different operations to be performed.

1. File-level manipulations, creating the new folders, deleting the old bundle file, and creating the core and peripheral files with their corresponding context
2. Link fixes to point to the new files.
3. Link fixes in the timeline, which is a JSON blob in a codeblock.

This decision may help for (2) and (3) as we can utilize those individual events and filter out things like code blocks, but manipulation of the form of (1) need to be done some other way due to the patching requirement.

Arguably, this is more robust for doing (2) and (3), since otherwise codeblocks will need to be explicitly filtered out with our own parsing.