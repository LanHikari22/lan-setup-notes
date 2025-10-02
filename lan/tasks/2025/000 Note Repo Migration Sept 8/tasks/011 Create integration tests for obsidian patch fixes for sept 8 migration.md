---
parent: '[[000 Note Repo Migration Sept 8]]'
spawned_by: '[[001 pulldown cmark to cmark escapes first obsidian tag on writeback]]'
context_type: task
status: done
---

Parent: [000 Note Repo Migration Sept 8](../000%20Note%20Repo%20Migration%20Sept%208.md)

Spawned by: [001 pulldown cmark to cmark escapes first obsidian tag on writeback](../issues/001%20pulldown%20cmark%20to%20cmark%20escapes%20first%20obsidian%20tag%20on%20writeback.md)

Spawned in: [<a name="spawn-task-b4d4c2" />^spawn-task-b4d4c2](../issues/001%20pulldown%20cmark%20to%20cmark%20escapes%20first%20obsidian%20tag%20on%20writeback.md#spawn-task-b4d4c2)

# 1 Journal

2025-09-24 Wk 39 Wed - 03:51 +03:00

Okay we created the first test for frontmatter, which just regressed when changing libraries.

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo test
````

````
failed with test data: frontmatter-000

<old>
---
status: todo
---
</old>


<new>
---
##status: todo
</new>
````

2025-09-24 Wk 39 Wed - 04:02 +03:00

````rust
        TestData::Identical {
            name: "frontmatter-000",
            data: r#"
                    @ ---
                    @ status: todo
                    @ ---
                "#
            .trim()
            .replace("@ ", "")
            .replace("                    ", ""),
        },
````

I use this as my newline solution, but it seems with formatting, the number of spaces changed and it somehow passed the test because it stopped treating that as an H2 header. Have to be careful with how these may change.

2025-09-24 Wk 39 Wed - 04:04 +03:00

````
failed with test data: frontmatter-001

<old>
---
parent: "[[000 Implement the Event Accumulator]]"
spawned_by: "[[000 Implement the Event Accumulator]]"
context_type: entry
---
</old>


<new>
---
##parent: "[[000 Implement the Event Accumulator]]"
spawned_by: "[[000 Implement the Event Accumulator]]"
context_type: entry
</new>
````

2025-09-24 Wk 39 Wed - 06:08 +03:00

Checked [stackoverflow answer](https://stackoverflow.com/a/58006287/6944447) for rust test initialization

2025-09-24 Wk 39 Wed - 06:12 +03:00

For `frontmatter-000`, we're getting,

````
[2025-09-24T03:11:29Z TRACE migration_rs::common] +0 "#"
[2025-09-24T03:11:29Z TRACE migration_rs::common] +0 "#"
````

So because it's a char differ, they're only appearing as individual `#`s.

2025-09-24 Wk 39 Wed - 06:19 +03:00

Changing it to diff on words helps this:

````
[2025-09-24T03:19:43Z TRACE migration_rs::common] +2 "##"
````

But we're still failing.

````
failed with test data: frontmatter-000

<old>
---
status: todo
---
</old>


<new>
---status: todo---
</new>
````

2025-09-24 Wk 39 Wed - 06:44 +03:00

Instead of the quotes for displaying the char/word diffs for `expt007`, let's make it so that the background color changes.

2025-09-24 Wk 39 Wed - 07:10 +03:00

We had to add a new line with the `---` added back as `\n---`.

2025-09-24 Wk 39 Wed - 07:11 +03:00

We're regressing on tables getting escaped. Need to create a test for it.

2025-09-24 Wk 39 Wed - 07:30 +03:00

````
failed with test data: table-000

<old>
| Simple | Table | Example |
| ------ | ----- | ------- |
| 0      | 1     | 2       |
| A      | B     | C       |
| `D`    | `E`   | `F`     |
| ⊕      | ☆     | ◯       |
</old>


<new>
\| Simple | Table | Example |
\| ------ | ----- | ------- |
\| 0      | 1     | 2       |
\| A      | B     | C       |
\| `D`    | `E`   | `F`     |
\| ⊕      | ☆     | ◯       |
</new>
````

````
[2025-09-24T04:34:45Z TRACE migration_rs::common] +0 "\|"
````

So this is the new word now.

![Pasted image 20250924073606.png](../../../../../attachments/Pasted%20image%2020250924073606.png)

2025-09-24 Wk 39 Wed - 07:45 +03:00

Failing on `table-002`,

This time, it treats most of the obsidian link as part of the diff...

````
[2025-09-24T04:38:33Z TRACE migration_rs::common] -2 "[[Summary-2025-09-01\|🕸️"
[2025-09-24T04:38:33Z TRACE migration_rs::common] +0 "[[Summary-2025-09-01|🕸️"

[2025-09-24T04:38:33Z TRACE migration_rs::common] -2 "[[Summary-2025-09-06\|📚📈🎲]]"
[2025-09-24T04:38:33Z TRACE migration_rs::common] +0 "[[Summary-2025-09-06|📚📈🎲]]"
````

We might have to pass it through word-diff filter followed by a secondary char-diff filter to accept the `\|` from the old content alone.

2025-09-24 Wk 39 Wed - 08:16 +03:00

Table tests pass with the second char-diff filter!

2025-09-24 Wk 39 Wed - 08:20 +03:00

![Pasted image 20250924082042.png](../../../../../attachments/Pasted%20image%2020250924082042.png)

````
diff --git a/lan/protos/2025/000 SpaceChem Controller/llm/01 Initial Exploration.md b/lan/protos/2025/000 SpaceChem Controller/llm/01 Initial Exploration.md
````

This time, dash bullets don't have spaces, and presentation-wise in obsidian, some empty lines are removed that mark paragraphs. Let's a make a test.

2025-09-24 Wk 39 Wed - 08:23 +03:00

````
diff --git a/lan/projects/2025/002 obsidian-sourced-website/tasks/2025/001 Create a reference basic website and host it with wasmer.md b/lan/projects/2025/002 obsidian-sourced-website/tasks/
````

````
In [Wasmer Distributed Networking (DNET)](https://docs.wasmer.io/edge/architecture#wasmer-distributed-networking-dnet), 
They mention that its principles include being fully stateless:

> Control planes add complexity and create single pointers of failure thus if one is able to deliver the same functionality without a control plane then it is a better design.

So this may not apply to nodes specifically.
````

It removed the `>` here.

2025-09-24 Wk 39 Wed - 08:50 +03:00

````
failed with test data: list-000

<old>
There is some text here.

*   **Some Text**: Some Description
*   More Text

Something new
</old>


<expected>
There is some text here.

- **Some Text**: Some Description
- More Text

Something new
</expected>


<new>
There is some text here.

-**Some Text**: Some Description
-More Text

Something new
</new>
````

2025-09-29 Wk 40 Mon - 20:54 +03:00

The above work now after adding a space after the `-` diff in patching

````
	<new_content>
    1. Some Item
       - And its description
    2. Another Item
       - And another description
    </new_content>
	
	<new_content1>
    1. Some Item
    - And its description
    2. Another Item
    - And another description
    </new_content1>
````

Our patching removed these spaces in the list.

This would not occur if we do 3 spaces per inner bullet. However, in Obsidian we use `\t` to tab the bullets.

2025-09-30 Wk 40 Tue - 03:15 +03:00

Under `Editor` in obsidian settings you can find the setting

![Pasted image 20250930031543.png](../../../../../attachments/Pasted%20image%2020250930031543.png)

If turned off, it will be four spaces on a tab Instead of a `\t`. Turning this off can make things more consistent, but [gh pulldown_cmark/pulldown_cmark](https://github.com/pulldown-cmark/pulldown-cmark/) still is using 3 spaces rather than 4.

2025-09-30 Wk 40 Tue03:21 +03:00

In the [options](https://github.com/pulldown-cmark/pulldown-cmark/blob/f4a326d225e79412b5ecabd1c241c851e8160815/pulldown-cmark/src/lib.rs#L664) for the pulldown_cmark parser,

They actually have [ENABLE_YAML_STYLE_METADATA_BLOCKS](https://github.com/pulldown-cmark/pulldown-cmark/blob/f4a326d225e79412b5ecabd1c241c851e8160815/pulldown-cmark/src/lib.rs#L709) for frontmatter support. So if we enable this, then we might not have to patch it.

[ENABLE_FOOTNOTES](https://github.com/pulldown-cmark/pulldown-cmark/blob/f4a326d225e79412b5ecabd1c241c851e8160815/pulldown-cmark/src/lib.rs#L686) might also help with disappearing unused footnotes with links.

2025-09-30 Wk 40 Tue - 03:47 +03:00

Spawn [010 Investigate pulldown-cmark-to-cmark adding 3 spaces for list items instead of 4](../investigations/010%20Investigate%20pulldown-cmark-to-cmark%20adding%203%20spaces%20for%20list%20items%20instead%20of%204.md) <a name="spawn-invst-52493f" />^spawn-invst-52493f

2025-09-30 Wk 40 Tue - 05:49 +03:00

So to be compliant with CommonMark, we need to have the number of spaces visually alined for inner bullets of numbered bullets. Obsidian does not do this.

2025-09-30 Wk 40 Tue - 06:11 +03:00

Checked [stackoverflow answer](https://stackoverflow.com/a/33159593/6944447) for diff use

````sh
git diff -U0 --word-diff-regex='[^[:space:]]' 
````

to shorten what we need to look into. It also uses `[-words-]` and `{+words+}` instead of line diffs which we could show in text, although I still prefer using `--color-words=.` to just get character differences.

2025-09-30 Wk 40 Tue - 06:18 +03:00

![Pasted image 20250930061841.png](../../../../../attachments/Pasted%20image%2020250930061841.png)

````
+++ a/lan/topics/read/papers/ai/entries/2025/000 Attention is all you need.md   
````

We need to change this manually, Each quote line needs to start with `> `.

2025-09-30 Wk 40 Tue - 06:26 +03:00

Turns out I accidentally add an extra ` ` at the end of the `Spawned by` with the extension. And this single-space change causes it here to remove the empty line between `Spawned by` and `Spawned in`

Spawn [012 Ensure spawned by line does not end with space after spawn command](012%20Ensure%20spawned%20by%20line%20does%20not%20end%20with%20space%20after%20spawn%20command.md) <a name="spawn-task-99507e" />^spawn-task-99507e

Added `spacing-000` test case for this.

2025-09-30 Wk 40 Tue - 06:36 +03:00

````
[2025-09-30T03:32:31Z TRACE migration_rs::common] -0.2 S "\s\n\n" len: 3
[2025-09-30T03:32:31Z TRACE migration_rs::common] +0.2 "\n\n" len: 2
````

````rust
let disp_s = s
	.replace("\t", "\\t")
	.replace("\n", "\\n")
	.replace(" ", "\\s");

// ...

} else {
	log_(&format!("-0.2 S \"{disp_s}\" len: {}", s.len()));
}

// ...

} else if s.trim() == "" && s.contains("\n") {
	log_(&format!("+0.2 \"{disp_s}\" len: {}", s.len()));
	mut_out += "\n";
````

We need to preserve not just a new line, but also the number of them.

````rust
} else if s.trim() == "" && s.contains("\n") {
	log_(&format!("+0.2 \"{disp_s}\" len: {}", s.len()));
	mut_out += &s.replace(" ", "",).replace("\t", "");
````

2025-09-30 Wk 40 Tue - 06:44 +03:00

So red is `[31m` and green is  `[32m`. Maybe we can just grep the output for these.

````sh
# in /home/lan/src/cloned/gh/deltatraced/delta-trace
git diff -U0 --color-words=. | grep '\[31m\|\[32m'
````

It's not very readable, but it shows that most changes are just trims of lines.

The changes I see are

1. Unnecessary `[...](<...>)` turned into `[...](...)` without the angle brackets.
1. Space trimming, mostly end of line, but sometimes it's end of file which only has an empty line or multiple.
1. Just a few files converting `*` to `-`  with proper tab spacing.

This seems acceptable. Let's commit the CommonMark writeback filtering.

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin app -- writeback /home/lan/src/cloned/gh/deltatraced/delta-trace/

# in /home/lan/src/cloned/gh/deltatraced/delta-trace
git add . 
git commit -m "filtering vault for commonmark writeback compliance"

# out
[main 86bbc53] filtering vault for commonmark writeback compliance
 172 files changed, 626 insertions(+), 1012 deletions(-)
````

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
git add .
git commit -m "commonmark to obsidian patch test 1st vault pass"

# out
Trim Trailing Whitespace.................................................Passed
Check Yaml...........................................(no files to check)Skipped
Check for added large files..............................................Passed
Check formatting.........................................................Passed
Run tests................................................................Passed
Check clippy lints.......................................................Passed
[main 978ad0a] commonmark to obsidian patch test 1st vault pass
 2 files changed, 144 insertions(+), 14 deletions(-)
````

2025-09-30 Wk 40 Tue - 07:03 +03:00

Now that `delta-trace` has been filtered for cmark compliance, let's run the filter again. This now should just be identity, no changes.

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin app -- writeback /home/lan/src/cloned/gh/deltatraced/delta-trace/
````

2025-09-30 Wk 40 Tue - 07:09 +03:00

But there's one change, an undesirable regression:

````diff
diff --git a/lan/topics/read/papers/ai/entries/2025/000 Attention is all you need.md b/lan/topics/read/papers/ai/entries/2025/000 Attention is all you need.md
index db9fcd1..931d20f 100644
--- a/lan/topics/read/papers/ai/entries/2025/000 Attention is all you need.md   
+++ b/lan/topics/read/papers/ai/entries/2025/000 Attention is all you need.md   
@@ -27,7 +27,7 @@ Skim through the paper and note foundational next concepts to learn about to und
 ^keyword-001
 
 > sequence modeling and transduction problems such as language modeling and machine translation
-> [[#^quote-paper]]
+>[[#^quote-paper]]
````

For some reason that space was removed. Let's add a test for this.

2025-09-30 Wk 40 Tue - 07:36 +03:00

Okay after some changes with the new lines and making quoted lines consistent, now we achieved identity on writeback for delta-trace!

though changes for that `quote-002` are a bit strange. For example if you remove all empty lines from `>` some empty space characters end up getting rejected:

````
<old>
>dominant sequence transduction models
>[[#^quote-paper]]

>Achieving {N} BLEU on the WMT 2014 English-to-German translation task
>[[#^quote-paper-paraphrase]]

>sequence modeling and transduction problems such as language modeling and machine translation
>[[#^quote-paper]]
</old>


<new>
>dominantsequence transduction models
>[[#^quote-paper]]

>Achieving {N} BLEU on the WMT 2014 English-to-German translation task
>[[#^quote-paper-paraphrase]]

>sequence modeling and transduction problems such as language modeling and machine translation
>[[#^quote-paper]]
</new>
````

But this wasn't an issue we found in the vault. So long as the spacing is consistent, we don't run into this.

2025-09-30 Wk 40 Tue - 08:09 +03:00

Now we should be ready for next steps beyond writeback!
