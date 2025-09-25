---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[010 Apply markdown writeback on delta-trace vault]]"
context_type: issue
status: todo
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[010 Apply markdown writeback on delta-trace vault]] 

Spawned in: [[010 Apply markdown writeback on delta-trace vault#^spawn-issue-9b47f5|^spawn-issue-9b47f5]]

# 1 Journal

2025-09-23 Wk 39 Tue - 05:41 +03:00

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin app -- writeback ~/src/cloned/gh/deltatraced/delta-trace
```

```diff
diff --git a/lan/docs/2025/000 Navigating Note Repositories.md b/lan/docs/2025/000 Navigating Note Repositories.md

-#lan #docs #external
+\#lan #docs #external
```

Testing with `expt000` and `expt006` similar to [[009 Investigate pulldown_cmark_to_cmark incorrectly converting item lists and bullet style]],

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt006_markdown_to_markdown <(cat << 'EOF'
#lan #docs #external
EOF
)

# out
\#lan #docs #external
```

Why is it escaping the first `#`?

This has consequences in obsidian.

![[Pasted image 20250923054830.png]]

2025-09-23 Wk 39 Tue - 05:50 +03:00

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt000_parse_single_pulldown_cmark <(cat << 'EOF'
#lan #docs #external
EOF
)

# out
Event Start(Paragraph)
Event Text(Borrowed("#lan #docs #external"))
Event End(Paragraph)
```

2025-09-23 Wk 39 Tue - 06:06 +03:00

Not finding anything relevant for this in [`pulldown_cmark_to_cmark -> Options`](https://github.com/Byron/pulldown-cmark-to-cmark/blob/bf34a3cac68e6f82a24ee3d44224a9e2ef2bcd0d/src/lib.rs#L180).

2025-09-23 Wk 39 Tue - 06:09 +03:00

This issue reproduces through the obsidian-export output too

```sh
# in /home/lan/src/cloned/gh/deltatraced/delta-trace
cat ~/src/cloned/gh/deltatraced/branches/delta-trace@webview/lan/docs/2025/000\ Navigating\ Note\ Repositories.md | less

# out (relevant)
\#lan #docs #external
```

`\#` is turned into `#` so I never noticed this in the webview on github. Though if you view the raw file, you would see it.

2025-09-23 Wk 39 Tue - 06:15 +03:00

This is not the only thing being escaped.

```diff
diff --git a/lan/docs/2025/000 Navigating Note Repositories.md b/lan/docs/2025/000 Navigating Note Repositories.md

-```sh
+````sh
 # in the note repository
 ls **/latest/
-```
+````

diff --git a/lan/docs/2025/001 Note heading categories and method.md b/lan/docs/2025/001 Note heading categories and method.md

-| Journal        | Mostly sequential logs of progress towards our objective
+\| Journal        | Mostly sequential logs of progress towards our objective

-- [x] Here we specify some objectives that has to be completed
+- \[x] Here we specify some objectives that has to be completed

diff --git a/lan/entries/monthly/2025/Mn 09 003 Ideas.md b/lan/entries/monthly/2025/Mn 09 003 Ideas.md

-[[Mn 09 September]]
+\[[Mn 09 September]]

```


Even exaclidraw files are being touched, which might not be good. Besides the number of ticks for code block, it is touching them for spacing.

2025-09-23 Wk 39 Tue - 06:23 +03:00

These are good changes:

```diff
diff --git a/lan/portfolio/Project - bn6f.md b/lan/portfolio/Project - bn6f.md

-- [Related IDA Analysis](<https://github.com/LanHikari22/GBA-IDA-Pseudo-Terminal>) 
+- [Related IDA Analysis](https://github.com/LanHikari22/GBA-IDA-Pseudo-Terminal)

diff --git a/lan/portfolio/Projects.md b/lan/portfolio/Projects.md

-
 Welcome! Let's walk through some of my work!

 ## 1.1 bn6f reverse engineering
+
 github: [bn6f](https://github.com/dism-exe/bn6f)

diff --git a/lan/docs/2025/001 Note heading categories and method.md b/lan/docs/2025/001 Note heading categories and method.md

-Order of the headings is by objectivity in scope. 
-1. Objective is most clear in scope. 
-2. Journal is clear progress logged towards the objective. 
-3. Tasks have a clear signal to completion, 
+Order of the headings is by objectivity in scope.
+
+1. Objective is most clear in scope.
+2. Journal is clear progress logged towards the objective.
+3. Tasks have a clear signal to completion,


```


2025-09-23 Wk 39 Tue - 06:27 +03:00

So the undesirable critical changes we're seeing are simple in form, although a direct replace may break the notes in this document when we apply the writeback here. But the escaped `#`, `|`, and `[` should be unescaped. And four ticks should become 3. We can apply this right after pulldown cmark to cmark finishes its rendering and see what we get.

```rust
// common cmark breaks some things for obsidian markdown notes. It escapes #, |, and [ and changes
// 3 codeblock ticks to 4.
mut_out = mut_out
	.replace("\\#", "#")
	.replace("\\|", "|")
	.replace("\\[", "[")
	.replace("````", "```");
```

2025-09-23 Wk 39 Tue - 06:32 +03:00

```diff
diff --git a/lan/docs/2025/001 Note heading categories and method.md b/lan/docs/2025/001 Note heading categories and method.md

 ## 5.1 Open the Windows Registery to fix Paint.exe!
 
-- [x] 
+- [x]
```

That space added was very intentional. Obsidian won't recognize it as a task otherwise. That said, that's a convention of the old format, and we don't use it anymore, so we can accept this.

It's still modifying excalidraw files. Let's skip any file with that in its name.

2025-09-23 Wk 39 Tue - 06:36 +03:00

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin app -- writeback ~/src/cloned/gh/deltatraced/delta-trace

# in /home/lan/src/cloned/gh/deltatraced/delta-trace
git status | wc -l

# out
235
```

Still many files, but less than before.

2025-09-23 Wk 39 Tue - 06:41 +03:00

```diff

diff --git a/lan/docs/2025/000 Navigating Note Repositories.md b/lan/docs/2025/000 Navigating Note Repositories.md

 ```sh
 scripts/templater/create_commands_for_user.sh "My UI Name" "username"
-```
+```
\ No newline at end of file
```

I guess it removes the new lines at the end of the file.

2025-09-23 Wk 39 Tue - 06:43 +03:00

Oops... Have to rename `lan/drawings/Wk 30 Drawing Math Book 000 Starting out.md` to have `excalidraw` in the name

Obsidian won't allow us to input `.excalidraw` but we can in the system rename it to an `.excalidraw.md` file.

2025-09-23 Wk 39 Tue - 06:50 +03:00

```diff
diff --git a/lan/entries/2025/000 Finding resource material for software engineering.md b/lan/entries/2025/000 Finding resource material for software engineering.md

 ---
-status: todo
----
+
+## status: todo
+
```

Right... It's not able to handle frontmatter either. We knew about this issue from [[003 Investigating frontmatter to markdown event mappings with pulldown_cmark]]

2025-09-23 Wk 39 Tue - 06:56 +03:00

It's messing up with Kanban files too, which again are managed and shouldn't be touched.

```diff
diff --git a/lan/projects/2025/001 teensy2-tiny-piano/tasks/Kanban.md b/lan/projects/2025/001 teensy2-tiny-piano/tasks/Kanban.md

-
-
 ## Todo
 
 - [ ] [[000 Get teensy2 embedded system to work again under docker]]
 
-
 ## Active
 
-
-
```


```diff
diff --git a/lan/projects/2025/002 obsidian-sourced-website/entries/2025/002 Learning about Astro and website deployment.md b/lan/projects/2025/002 obsidian-sourced-website/entries/2025/002 


-> Bulma is a **CSS** framework. As such, the sole output is a single CSS file: [bulma.css](https://github.com/jgthms/bulma/blob/main/css/bulma.css)
+ > 
+ > Bulma is a **CSS** framework. As such, the sole output is a single CSS file: [bulma.css](https://github.com/jgthms/bulma/blob/main/css/bulma.css)
```

It wants an empty quote line first?

2025-09-23 Wk 39 Tue - 07:17 +03:00

```diff
diff --git a/lan/overview/monthly/2025/Mn 09 September.md b/lan/overview/monthly/2025/Mn 09 September.md

-| [[Mn 09 000 Learning\|Learning]]         | 2           | Capturing highlights of practices and lessons learned this month!                                                                 
+| [[Mn 09 000 Learning|Learning]]         | 2           | Capturing highlights of practices and lessons learned this month!                                                                                                                    |
```

Well... Sometimes obsidian itself *does use* an escaped bar. We probably should replace what is changed by the script, and nothing else. 

2025-09-23 Wk 39 Tue - 08:05 +03:00

```diff
diff --git a/lan/overview/monthly/2025/Mn 09 September.md b/lan/overview/monthly/2025/Mn 09 September.md

-| [[Mn 09 000 Learning\|Learning]]         | 2           | Capturing highlights of practices and lessons learned this month!                                                                                                                    |
+| [[Mn 09 000 Learning     | Learning]]     | 2       | Capturing highlights of practices and lessons learned this month!                                                                                                                    |
```

It's padding the links themselves for tables...

2025-09-23 Wk 39 Tue - 08:22 +03:00

```diff
diff --git a/lan/protos/2025/001 Rust Diesel Event Sourcing/goals/2025/000 Minimal Credit Store Demo.md b/lan/protos/2025/001 Rust Diesel Event Sourcing/goals/2025/000 Minimal Credit Store Demo.md

 **User-facing**
 
 - [ ] User can add, delete, and edit new persons
+
 - [ ] User can add credit expenses and income
 
 - [ ] User is able to start a new report and new expenses automatically go there
+
 - [ ] User is able to conclude a report, and its aggregate is automatically produced
 
 - [ ] User is able to create chains of reports whose aggregates are summed into parent report levels
+
 - [ ] User can export a summary report for a given report level which only aggregates report levels higher than it
 
 - [ ] Besides credits, user is also able to manage arcade coins in a similar fashion to credits with their own reports
```

Why the new lines? This changes presentation.

```diff
diff --git a/lan/protos/2025/001 Rust Diesel Event Sourcing/goals/2025/000 Minimal Credit Store Demo.md b/lan/protos/2025/001 Rust Diesel Event Sourcing/goals/2025/000 Minimal Credit Store Demo.md

 **Developer-facing**
 
-- [ ] Developer can add new managed tables in a similar fashion to credit or coin with minimal setup
-
+- \[ ] Developer can add new managed tables in a similar fashion to credit or coin with minimal setup
\ No newline at end of file

```

And why does it escape this only when it's a new line? 

2025-09-23 Wk 39 Tue - 09:25 +03:00

Let's use [docs.rs text-diff](https://docs.rs/text-diff/latest/text_diff/index.html) to modify or reject changes via a changeset rather than direct line comparisons. 

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo add text-diff
```

Spawn [[002 Can impl a cmark to obsidian markdown crate]] ^spawn-idea-51f0eb

2025-09-23 Wk 39 Tue - 09:39 +03:00

Let's create `expt007` to view the outputs of text diff

2025-09-23 Wk 39 Tue - 09:48 +03:00

Let's keep a `~/tmp/del/orig.md`  and `~/tmp/del/edit.md`. 

```sh
rm -rf ~/tmp/del/*

# in /home/lan/src/cloned/gh/deltatraced/delta-trace
git reset --hard
cp lan/overview/monthly/2025/Mn\ 09\ September.md ~/tmp/del/orig.md

# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin app -- writeback /home/lan/src/cloned/gh/deltatraced/delta-trace/

# in /home/lan/src/cloned/gh/deltatraced/delta-trace
cp lan/overview/monthly/2025/Mn\ 09\ September.md ~/tmp/del/edit.md
```

2025-09-23 Wk 39 Tue - 09:53 +03:00

We can now compare the diff we get with `diff`, and our text-diff crate.

```sh
diff -u ~/tmp/del/orig.md ~/tmp/del/edit.md | less
```

2025-09-23 Wk 39 Tue - 18:11 +03:00

We could also do

```sh
git diff --no-index ~/tmp/del/orig.md ~/tmp/del/edit.md --diff-algorithm myers
```

using `git diff` outside a git repo as learned through this [post](https://nickjanetakis.com/blog/git-diff-can-be-used-outside-of-a-git-repo-and-it-has-useful-features)

We can also see character diff highlighting:

```sh
git diff --no-index ~/tmp/del/orig.md ~/tmp/del/edit.md --diff-algorithm myers --color-words=.
```

2025-09-23 Wk 39 Tue - 19:14 +03:00

Created `expt008` to compare writeback + patching solution without having to run against the entire vault

```sh
diff -u ~/tmp/del/orig.md <(cargo run --bin expt008_fix_obsidian_markdown ~/tmp/del/orig.md)
cargo run --bin expt007_text_diff ~/tmp/del/orig.md <(cargo run --bin expt008_fix_obsidian_markdown ~/tmp/del/orig.md) | less
```

2025-09-23 Wk 39 Tue - 19:23 +03:00

I'm not seeing indication of aligning mid-link like before

![[Pasted image 20250923192358.png]]

2025-09-23 Wk 39 Tue - 20:40 +03:00

![[Pasted image 20250923204008.png]]

We may be able to reject frontmatter modification, since it just removes a `---` and adds a `##`. 

2025-09-23 Wk 39 Tue - 20:42 +03:00

Gotta be careful not to accidentally reset hard our migration scripts... Luckily vscode stores the changes still.

2025-09-23 Wk 39 Tue - 20:48 +03:00

Need to not include more managed files. `Summarize`, `Summary`, and `Timeline`.

2025-09-23 Wk 39 Tue - 20:54 +03:00

![[Pasted image 20250923205446.png]]

```
diff --git a/lan/entries/monthly/2025/Mn 09 001 Resources.md b/lan/entries/monthly/2025/Mn 09 001 Resources.md
```

Math changes...

From
$$
{\frac {\mathrm {d} f}{dx}}=\sum _{i=1}^{n}{\frac {\partial f}{\partial x_{i}}}\,{\frac {\mathrm {d} x_{i}}{\mathrm {d} x}}
$$

To

$$
{\frac {\mathrm {d} f}{dx}}=\sum *{i=1}^{n}{\frac {\partial f}{\partial x*{i}}}\,{\frac {\mathrm {d} x_{i}}{\mathrm {d} x}}
$$

Why?

Let's keep removals `_` and remove additions `*`

2025-09-23 Wk 39 Tue - 20:59 +03:00

![[Pasted image 20250923205929.png]]

```
diff --git a/lan/entries/2025/001 Sept 8 Obsidian note process change.md b/lan/entries/2025/001 Sept 8 Obsidian note process change.md
```

Why are they still able to add the `\` in 

```
         - \[ ] Allows the user to specify the category by selection (tasks/issues/ ...)
```

2025-09-23 Wk 39 Tue - 21:24 +03:00

Even after trimming it still happens.

```sh
# in /home/lan/src/cloned/gh/deltatraced/delta-trace
git reset --hard
cp lan/entries/2025/001\ Sept\ 8\ Obsidian\ note\ process\ change.md ~/tmp/del/orig.md
```

2025-09-23 Wk 39 Tue - 21:35 +03:00

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt007_text_diff ~/tmp/del/orig.md <(cargo run --bin expt008_fix_obsidian_markdown ~/tmp/del/orig.md) | less
```

![[Pasted image 20250923213514.png]]

It's `- \`. 

2025-09-23 Wk 39 Tue - 21:51 +03:00

![[Pasted image 20250923215115.png]]

```
diff --git a/lan/protos/2025/000 SpaceChem Controller/llm/01 Initial Exploration.md b/lan/protos/2025/000 SpaceChem Controller/llm/01 Initial Exploration.md
```

The tabbing doesn't seem to change the presentation here. I don't mind `*` $\to$ `-`.

2025-09-23 Wk 39 Tue - 22:16 +03:00

![[Pasted image 20250923221659.png]]

```
diff --git a/lan/topics/read/papers/ai/entries/2025/000 Attention is all you need.md b/lan/topics/read/papers/ai/entries/2025/000 Attention is all you need.md
```

- [ ] Unresolved escaping in links in quotes

2025-09-23 Wk 39 Tue - 22:46 +03:00

```sh
# in /home/lan/src/cloned/gh/deltatraced/delta-trace
git reset --hard
cp "lan/topics/read/papers/ai/entries/2025/000 Attention is all you need.md" ~/tmp/del/orig.md
```

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt007_text_diff ~/tmp/del/orig.md <(cargo run --bin expt008_fix_obsidian_markdown ~/tmp/del/orig.md) | less
```

![[Pasted image 20250923225430.png]]

I fixed these manually, there really should have been a quoted line there.


2025-09-23 Wk 39 Tue - 22:21 +03:00

![[Pasted image 20250923222109.png]]

```
diff --git a/lan/topics/read/papers/ai/entries/2025/000 Attention is all you need.md b/lan/topics/read/papers/ai/entries/2025/000 Attention is all you need.md
```

- [ ] Removed slash from multiline math

2025-09-23 Wk 39 Tue - 22:27 +03:00

- [ ] Lots of slashes being removed in 

```
diff --git a/lan/topics/study/books/math/2025/001 Probability - Theory and Examples/entries/2025/000 Starting out Probability Theory and Examples.md b/lan/topics/study/books/math/2025/001 Pr
```

2025-09-23 Wk 39 Tue - 22:29 +03:00

Need to also ignore anything in templater like commands

2025-09-24 Wk 39 Wed - 01:57 +03:00

```rust
// in adhoc_fix_rendered_markdown_output_for_obsidian

if s.trim() != "\\"
	&& s.trim() != ""
	&& s.trim() != "`"
	&& s.trim() != "##"
	&& s.trim() != "*"
	&& s.trim() != ">"
	&& s.trim() != "- \\"
{
	log::trace!("+0 \"{s}\"");
	mut_out += &s;
} else if s.trim() == "- \\" {
	log::trace!("+1 \"{s}\"");
	mut_out += &s.replace("\\", "");
} else {
	log::trace!("+2 \"{s}\"");
}
```

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt008_fix_obsidian_markdown ~/tmp/del/orig.md

# out (relevant)
[2025-09-23T22:55:16Z TRACE migration_rs::common] +2 "\"
[2025-09-23T22:55:16Z TRACE migration_rs::common] +2 "\"
```

That shows it's skipping it

![[Pasted image 20250924021016.png]]

The issue is that the old content chunks are big according to this text diff.

2025-09-24 Wk 39 Wed - 02:26 +03:00

In [`pulldown_cmark_to_cmark -> Options`](https://github.com/Byron/pulldown-cmark-to-cmark/blob/bf34a3cac68e6f82a24ee3d44224a9e2ef2bcd0d/src/lib.rs#L180),

```
code_block_token_count: 4,
```

This should make it so that don't have to worry about the codeblock ticks, just change it to 3.

2025-09-24 Wk 39 Wed - 02:47 +03:00

Let's try another diffing crate. The issue before is due to the diffs being created themselves not matching myers' diff algorithm.

Let's try [docs.rs similar](https://docs.rs/similar/latest/similar/). It explicitly mentions supporting myers'.

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo add similar

# out (relevant)
Adding similar v2.7.0 to dependencies
	 Features:
	 + text
	 - bstr
	 - bytes
	 - inline
	 - serde
	 - unicode
	 - unicode-segmentation
	 - wasm32_web_time
	 - web-time
```

2025-09-24 Wk 39 Wed - 03:00 +03:00

The [default algorithm](https://github.com/mitsuhiko/similar/blob/a169e29954502d67e26c9e766f2f719e99a52559/src/types.rs#L24) used by TextDiff is myers.

2025-09-24 Wk 39 Wed - 03:08 +03:00

To use `expt007` with the previous text-diff dependency, use the version from `c7be439`.

2025-09-24 Wk 39 Wed - 03:17 +03:00

frontmatter broke again. We need to create tests for small writeback cases we encounter.

Spawn [[002 vscode rust analyzer does not work in integration test directory]] ^spawn-issue-07b5a7

Spawn [[011 Create integration tests for obsidian patch fixes for sept 8 migration]] ^spawn-task-b4d4c2
