

 <file ./lan/tasks/2025/000 Note Repo Migration Sept 8/tasks/013 Change all delta-trace old format notes into note clusters without applying link fixing.md>

---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[009 Impl and carry out cluster note migration]]"
context_type: task
status: todo
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[009 Impl and carry out cluster note migration]]

Spawned in: [[009 Impl and carry out cluster note migration#^spawn-task-479e16|^spawn-task-479e16]]

# 1 Journal

2025-09-30 Wk 40 Tue - 08:52 +03:00

The worry now is the fact that old content is necessary to have for cmark $\to$ obsidian markdown patching, we likely will have to split the file at the text level and not just the event level. It is however possible that the patching rejects anything but the relevant window, so we can try that.

2025-09-30 Wk 40 Tue - 09:11 +03:00

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin app -- extract_old_format_records /home/lan/src/cloned/gh/deltatraced/delta-trace/

# out (error, relevant)
[2025-09-30T06:18:29Z INFO  app] processing "/home/lan/src/cloned/gh/deltatraced/delta-trace/lan/topics/practice/ctf/topic/entries/2025/000 Solving Mountain and Dragon CTF.md"

thread 'main' panicked at src/bin/app.rs:98:64:
Could not process links: LinkExtractError(MustHaveZeroOrOneBar(3, "    command = (\n        commands\n        | pipe.OfIter[CommandData].filter(\n            lambda command: command.activation_byte == command_byte\n        )\n        | pipe.OfIter[CommandData].to_list()\n        | pipe.Of[List[CommandData"))
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```


2025-09-30 Wk 40 Tue - 09:23 +03:00

This is in a code block, there cannot exist links there. And we already make sure to only process `Text` events, so what gives?

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt000_parse_single_pulldown_cmark "/home/lan/src/cloned/gh/deltatraced/delta-trace/lan/topics/practice/ctf/topic/entries/2025/000 Solving Mountain and Dragon CTF.md" | less

# out (relevant)
Event Start(CodeBlock(Fenced(Borrowed("py"))))
Event Text(Borrowed("    command = (\n        commands\n        | pipe.OfIter[CommandData].filter(\n            lambda command: command.activation_byte == command_byte\n        )\n        | pipe.OfIter[CommandData].to_list()\n        | pipe.Of[List[CommandData]].map(lambda lst: lst[0] if len(lst) == 1 else None)\n    )\n\n    if command is None:\n        return (None, tape)\n"))
Event End(CodeBlock)
```

We need to track that a code block tag is not currently open.

2025-09-30 Wk 40 Tue - 09:34 +03:00

This is both for `fn extract_obsidian_md_links` and `fn extract_linkable_obsidian_md_items`

2025-09-30 Wk 40 Tue - 09:44 +03:00

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin app -- extract_old_format_records /home/lan/src/cloned/gh/deltatraced/delta-trace/

# out (relevant)
[2025-09-30T06:39:14Z INFO  app] processing "/home/lan/src/cloned/gh/deltatraced/delta-trace/lan/topics/practice/tutorials/ai/tensorflow.org/entries/2025/000 Transformer Tensorflow Tutorial.md"
[2025-09-30T06:39:14Z INFO  app] processing "/home/lan/src/cloned/gh/deltatraced/delta-trace/lan/topics/practice/ctf/topic/entries/2025/000 Solving Mountain and Dragon CTF.md"
[2025-09-30T06:43:05Z INFO  app] processing "/home/lan/src/cloned/gh/deltatraced/delta-trace/lan/topics/practice/ctf/2025/google-ctf-2025/during/entries/000 overall.md"

[2025-09-30T06:43:05Z INFO  app] processing "/home/lan/src/cloned/gh/deltatraced/delta-trace/lan/topics/practice/ctf/2025/google-ctf-2025/during/topics/reversing/entries/000 Multiarch.md"
[2025-09-30T06:43:05Z ERROR migration_rs::cluster_note] Could not get any content within H2: [Start(Heading { level: H2, id: None, classes: [], attrs: [] }), Text(Borrowed("3.1 Setup ")), Start(Link { link_type: Inline, dest_url: Borrowed("https://github.com/VirusTotal/yara"), title: Borrowed(""), id: Borrowed("") }), Text(Borrowed("yara")), End(Link)]
```

So two things. One, I thought it wasn't terminating because it took 4 minutes to process that CTF file for some reason. And two, the new error.

2025-09-30 Wk 40 Tue - 10:09 +03:00

```
## 3.1 Setup [yara](https://github.com/VirusTotal/yara)
```

We should not have links in headings. Change that manually.

2025-09-30 Wk 40 Tue - 10:14 +03:00

Let's run it in release to be faster.

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --release --bin app -- extract_old_format_records /home/lan/src/cloned/gh/deltatraced/delta-trace/


# out (relevant)
[2025-09-30T07:14:48Z INFO  app] processing "/home/lan/src/cloned/gh/deltatraced/delta-trace/lan/topics/practice/ctf/topic/entries/2025/000 Solving Mountain and Dragon CTF.md"
[2025-09-30T07:15:10Z INFO  app] processing "/home/lan/src/cloned/gh/deltatraced/delta-trace/lan/topics/practice/ctf/2025/google-ctf-2025/during/entries/000 overall.md"

[2025-09-30T07:15:10Z INFO  app] processing "/home/lan/src/cloned/gh/deltatraced/delta-trace/lan/topics/practice/ctf/2025/google-ctf-2025/during/topics/crypto/entries/003 NUMEROLOGY.md"
[2025-09-30T07:15:10Z ERROR migration_rs::cluster_note] Could not get any content within H2: [Start(Heading { level: H2, id: None, classes: [], attrs: [] }), Text(Borrowed("4.1 Setup ")), Start(Link { link_type: Inline, dest_url: Borrowed("https://github.com/RustCrypto/stream-ciphers/tree/master/chacha20"), title: Borrowed(""), id: Borrowed("") }), Text(Borrowed("gh RustCrypto chacha20")), End(Link)]
```

It's faster, though still takes noticably more than the others.

Fixing more links in headings. We're also running writeback and then commiting so that writeback would yield identity and not interfere with diffs.

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin app -- writeback /home/lan/src/cloned/gh/deltatraced/delta-trace/
```

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --release --bin app -- extract_old_format_records /home/lan/src/cloned/gh/deltatraced/delta-trace/


# out (error, relevant)
[2025-09-30T07:19:55Z INFO  app] processing "/home/lan/src/cloned/gh/deltatraced/delta-trace/lan/topics/study/books/math/2025/001 Probability - Theory and Examples/entries/2025/000 Starting out Probability Theory and Examples.md"

thread 'main' panicked at src/bin/app.rs:98:64:
Could not process links: LinkExtractError(NoBracketsFound("[[018 σ-additive|σ-additivity]], "))
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```

2025-09-30 Wk 40 Tue - 10:27 +03:00

When parsing links we expected

```rust
if !s.starts_with("[[") || !s.ends_with("]]") {
	return Err(ObsidianLinkParseError::NoBracketsFound(s.to_owned()));
}
```

But in this case `,`  came along. This error name should also be clarified. There are brackets, but it is true that it's not enclosed in them.

2025-09-30 Wk 40 Tue - 10:46 +03:00

Adding some traces,

```rust
// in fn parse_multiple_obsidian_links
let tokens = s
	.split("[[")
	.filter(|s| s.contains("]]"))
	.map(|s_split| {
		let end_idx = s_split
			.find("]]")
			.ok_or(ObsidianLinkParseAssertError::FilteredForEndBracketsMustBeFound)?;

		let stripped = s_split.chars().take(end_idx + "]]".len()).join("");

		log::trace!("s: {s_split}");
		log::trace!("stripped: {stripped}");

		// It will already include ]], but we have to put the [[ back
		Ok(format!("[[{stripped}"))
	})
	.collect::<Result<Vec<_>, ObsidianLinkParseError>>()?;
```

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --release --bin app -- -vv  extract_old_format_records /home/lan/src/cloned/gh/deltatraced/delta-trace/

# out (error, relevant)
[2025-09-30T07:45:43Z TRACE migration_rs::common] s: 018 σ-additive|σ-additivity]], which means that for the corresponding set, when you take the measure of the union of two sets, it corresponds to adding the measures of each:
[2025-09-30T07:45:43Z TRACE migration_rs::common] stripped: 018 σ-additive|σ-additivity]], 

thread 'main' panicked at src/bin/app.rs:98:64:
Could not process links: LinkExtractError(NotEnclosedInBrackets("[[018 σ-additive|σ-additivity]], "))
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```



 </file ./lan/tasks/2025/000 Note Repo Migration Sept 8/tasks/013 Change all delta-trace old format notes into note clusters without applying link fixing.md>



 <file ./lan/tasks/2025/000 Note Repo Migration Sept 8/tasks/011 Create integration tests for obsidian patch fixes for sept 8 migration.md>

---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[001 pulldown cmark to cmark escapes first obsidian tag on writeback]]"
context_type: task
status: done
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[001 pulldown cmark to cmark escapes first obsidian tag on writeback]] 

Spawned in: [[001 pulldown cmark to cmark escapes first obsidian tag on writeback#^spawn-task-b4d4c2|^spawn-task-b4d4c2]]

# 1 Journal

2025-09-24 Wk 39 Wed - 03:51 +03:00

Okay we created the first test for frontmatter, which just regressed when changing libraries.

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo test
```

```
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
```

2025-09-24 Wk 39 Wed - 04:02 +03:00

```rust
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
```

I use this as my newline solution, but it seems with formatting, the number of spaces changed and it somehow passed the test because it stopped treating that as an H2 header. Have to be careful with how these may change.

2025-09-24 Wk 39 Wed - 04:04 +03:00

```
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
```

2025-09-24 Wk 39 Wed - 06:08 +03:00

Checked [stackoverflow answer](https://stackoverflow.com/a/58006287/6944447) for rust test initialization

2025-09-24 Wk 39 Wed - 06:12 +03:00

For `frontmatter-000`, we're getting,

```
[2025-09-24T03:11:29Z TRACE migration_rs::common] +0 "#"
[2025-09-24T03:11:29Z TRACE migration_rs::common] +0 "#"
```

So because it's a char differ, they're only appearing as individual `#`s. 

2025-09-24 Wk 39 Wed - 06:19 +03:00

Changing it to diff on words helps this:

```
[2025-09-24T03:19:43Z TRACE migration_rs::common] +2 "##"
```

But we're still failing.

```
failed with test data: frontmatter-000

<old>
---
status: todo
---
</old>


<new>
---status: todo---
</new>
```

2025-09-24 Wk 39 Wed - 06:44 +03:00

Instead of the quotes for displaying the char/word diffs for `expt007`, let's make it so that the background color changes.

2025-09-24 Wk 39 Wed - 07:10 +03:00

We had to add a new line with the `---` added back as `\n---`. 

2025-09-24 Wk 39 Wed - 07:11 +03:00

We're regressing on tables getting escaped. Need to create a test for it.

2025-09-24 Wk 39 Wed - 07:30 +03:00

```
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
```

```
[2025-09-24T04:34:45Z TRACE migration_rs::common] +0 "\|"
```

So this is the new word now.

![[Pasted image 20250924073606.png]]


2025-09-24 Wk 39 Wed - 07:45 +03:00

Failing on `table-002`,

This time, it treats most of the obsidian link as part of the diff...

```
[2025-09-24T04:38:33Z TRACE migration_rs::common] -2 "[[Summary-2025-09-01\|🕸️"
[2025-09-24T04:38:33Z TRACE migration_rs::common] +0 "[[Summary-2025-09-01|🕸️"

[2025-09-24T04:38:33Z TRACE migration_rs::common] -2 "[[Summary-2025-09-06\|📚📈🎲]]"
[2025-09-24T04:38:33Z TRACE migration_rs::common] +0 "[[Summary-2025-09-06|📚📈🎲]]"
```

We might have to pass it through word-diff filter followed by a secondary char-diff filter to accept the `\|` from the old content alone.

2025-09-24 Wk 39 Wed - 08:16 +03:00

Table tests pass with the second char-diff filter!

2025-09-24 Wk 39 Wed - 08:20 +03:00

![[Pasted image 20250924082042.png]]

```
diff --git a/lan/protos/2025/000 SpaceChem Controller/llm/01 Initial Exploration.md b/lan/protos/2025/000 SpaceChem Controller/llm/01 Initial Exploration.md
```

This time, dash bullets don't have spaces, and presentation-wise in obsidian, some empty lines are removed that mark paragraphs. Let's a make a test.

2025-09-24 Wk 39 Wed - 08:23 +03:00

```
diff --git a/lan/projects/2025/002 obsidian-sourced-website/tasks/2025/001 Create a reference basic website and host it with wasmer.md b/lan/projects/2025/002 obsidian-sourced-website/tasks/
```

```
In [Wasmer Distributed Networking (DNET)](https://docs.wasmer.io/edge/architecture#wasmer-distributed-networking-dnet), 
They mention that its principles include being fully stateless:

> Control planes add complexity and create single pointers of failure thus if one is able to deliver the same functionality without a control plane then it is a better design.

So this may not apply to nodes specifically.
```

It removed the `>` here.

2025-09-24 Wk 39 Wed - 08:50 +03:00

```
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
```

2025-09-29 Wk 40 Mon - 20:54 +03:00

The above work now after adding a space after the `-` diff in patching

```
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
```

Our patching removed these spaces in the list.

This would not occur if we do 3 spaces per inner bullet. However, in Obsidian we use `\t` to tab the bullets.

2025-09-30 Wk 40 Tue - 03:15 +03:00

Under `Editor` in obsidian settings you can find the setting

![[Pasted image 20250930031543.png]]

If turned off, it will be four spaces on a tab Instead of a `\t`. Turning this off can make things more consistent, but [gh pulldown_cmark/pulldown_cmark](https://github.com/pulldown-cmark/pulldown-cmark/) still is using 3 spaces rather than 4.

2025-09-30 Wk 40 Tue03:21 +03:00

In the [options](https://github.com/pulldown-cmark/pulldown-cmark/blob/f4a326d225e79412b5ecabd1c241c851e8160815/pulldown-cmark/src/lib.rs#L664) for the pulldown_cmark parser, 

They actually have [ENABLE_YAML_STYLE_METADATA_BLOCKS](https://github.com/pulldown-cmark/pulldown-cmark/blob/f4a326d225e79412b5ecabd1c241c851e8160815/pulldown-cmark/src/lib.rs#L709) for frontmatter support. So if we enable this, then we might not have to patch it.

[ENABLE_FOOTNOTES](https://github.com/pulldown-cmark/pulldown-cmark/blob/f4a326d225e79412b5ecabd1c241c851e8160815/pulldown-cmark/src/lib.rs#L686) might also help with disappearing unused footnotes with links.

2025-09-30 Wk 40 Tue - 03:47 +03:00

Spawn [[010 Investigate pulldown-cmark-to-cmark adding 3 spaces for list items instead of 4]] ^spawn-invst-52493f

2025-09-30 Wk 40 Tue - 05:49 +03:00

So to be compliant with CommonMark, we need to have the number of spaces visually alined for inner bullets of numbered bullets. Obsidian does not do this. 

2025-09-30 Wk 40 Tue - 06:11 +03:00

Checked [stackoverflow answer](https://stackoverflow.com/a/33159593/6944447) for diff use

```sh
git diff -U0 --word-diff-regex='[^[:space:]]' 
```

to shorten what we need to look into. It also uses `[-words-]` and `{+words+}` instead of line diffs which we could show in text, although I still prefer using `--color-words=.` to just get character differences.

2025-09-30 Wk 40 Tue - 06:18 +03:00

![[Pasted image 20250930061841.png]]

```
+++ a/lan/topics/read/papers/ai/entries/2025/000 Attention is all you need.md   
```

We need to change this manually, Each quote line needs to start with `> `.

2025-09-30 Wk 40 Tue - 06:26 +03:00

Turns out I accidentally add an extra ` ` at the end of the `Spawned by` with the extension. And this single-space change causes it here to remove the empty line between `Spawned by` and `Spawned in`

Spawn [[012 Ensure spawned by line does not end with space after spawn command]] ^spawn-task-99507e

Added `spacing-000` test case for this.

2025-09-30 Wk 40 Tue - 06:36 +03:00

```
[2025-09-30T03:32:31Z TRACE migration_rs::common] -0.2 S "\s\n\n" len: 3
[2025-09-30T03:32:31Z TRACE migration_rs::common] +0.2 "\n\n" len: 2
```

```rust
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
```

We need to preserve not just a new line, but also the number of them. 

```rust
} else if s.trim() == "" && s.contains("\n") {
	log_(&format!("+0.2 \"{disp_s}\" len: {}", s.len()));
	mut_out += &s.replace(" ", "",).replace("\t", "");
```

2025-09-30 Wk 40 Tue - 06:44 +03:00

So red is `[31m` and green is  `[32m`. Maybe we can just grep the output for these.

```sh
# in /home/lan/src/cloned/gh/deltatraced/delta-trace
git diff -U0 --color-words=. | grep '\[31m\|\[32m'
```

It's not very readable, but it shows that most changes are just trims of lines.

The changes I see are

1. Unnecessary `[...](<...>)` turned into `[...](...)` without the angle brackets.
2. Space trimming, mostly end of line, but sometimes it's end of file which only has an empty line or multiple.
3.  Just a few files converting `*` to `-`  with proper tab spacing.

This seems acceptable. Let's commit the CommonMark writeback filtering.

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin app -- writeback /home/lan/src/cloned/gh/deltatraced/delta-trace/

# in /home/lan/src/cloned/gh/deltatraced/delta-trace
git add . 
git commit -m "filtering vault for commonmark writeback compliance"

# out
[main 86bbc53] filtering vault for commonmark writeback compliance
 172 files changed, 626 insertions(+), 1012 deletions(-)
```

```sh
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
```

2025-09-30 Wk 40 Tue - 07:03 +03:00

Now that `delta-trace` has been filtered for cmark compliance, let's run the filter again. This now should just be identity, no changes.

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin app -- writeback /home/lan/src/cloned/gh/deltatraced/delta-trace/
```

2025-09-30 Wk 40 Tue - 07:09 +03:00

But there's one change, an undesirable regression:

```diff
diff --git a/lan/topics/read/papers/ai/entries/2025/000 Attention is all you need.md b/lan/topics/read/papers/ai/entries/2025/000 Attention is all you need.md
index db9fcd1..931d20f 100644
--- a/lan/topics/read/papers/ai/entries/2025/000 Attention is all you need.md   
+++ b/lan/topics/read/papers/ai/entries/2025/000 Attention is all you need.md   
@@ -27,7 +27,7 @@ Skim through the paper and note foundational next concepts to learn about to und
 ^keyword-001
 
 > sequence modeling and transduction problems such as language modeling and machine translation
-> [[#^quote-paper]]
+>[[#^quote-paper]]
```

For some reason that space was removed. Let's add a test for this.

2025-09-30 Wk 40 Tue - 07:36 +03:00

Okay after some changes with the new lines and making quoted lines consistent, now we achieved identity on writeback for delta-trace!

though changes for that `quote-002` are a bit strange. For example if you remove all empty lines from `>` some empty space characters end up getting rejected:

```
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
```

But this wasn't an issue we found in the vault. So long as the spacing is consistent, we don't run into this.

2025-09-30 Wk 40 Tue - 08:09 +03:00

Now we should be ready for next steps beyond writeback!

 </file ./lan/tasks/2025/000 Note Repo Migration Sept 8/tasks/011 Create integration tests for obsidian patch fixes for sept 8 migration.md>



 <file ./lan/tasks/2025/000 Note Repo Migration Sept 8/tasks/000 Create Obsidian extension for actions.md>

---
parent: "[[000 Note Repo Migration Sept 8]]"
context_type: task
status: done
---

#proj 

Parent: [[000 Note Repo Migration Sept 8]]

# 1 Objective

We need to have an obsidian extension to be able to run commands for task creation automation amongst other things. 

# 2 Related

This is a re-creation of [[002 Create an experimental obsidian extension to test ideas in]].
# 3 Journal

2025-09-10 Wk 37 Wed - 15:26 +03:00

Obsidian documentation can be found on this in [1](https://docs.obsidian.md/Plugins/Getting+started/Build+a+plugin) and [2](https://publish.obsidian.md/hub/04+-+Guides%2C+Workflows%2C+%26+Courses/Guides/How+to+get+started+developing+plugins).

There is a [getting started post](https://dev.to/bjarnerentz/journey-developing-an-obsidian-plugin-part-1-getting-started-53m6) on learnings and also links to [gh obsidianmd/obsidian-sample-plugin](https://github.com/obsidianmd/obsidian-sample-plugin).

2025-09-10 Wk 37 Wed - 15:41 +03:00

So following [obsidian docs build a plugin](https://docs.obsidian.md/Plugins/Getting+started/Build+a+plugin),

Let's create a project from [gh obsidianmd/obsidian-sample-plugin](https://github.com/obsidianmd/obsidian-sample-plugin) template.  Let's just create a `lan-obsidian-plugin` to test things for now. 

It exists: [gh LanHikari22/lan-obsidian-plugin](https://github.com/LanHikari22/lan-obsidian-plugin)

Clone it on my system:

```sh
git clone git@github.com:LanHikari22/lan-obsidian-plugin.git ~/src/cloned/gh/LanHikari22/lan-obsidian-plugin
cd ~/src/cloned/gh/LanHikari22/lan-obsidian-plugin
```

The README mentions to run `npm i` in the repo. It also mentions to check that `node --version` is at least 16, and mine is `v24.1.0` while my `npm --version` is `11.5.2`.

```sh
# in /home/lan/src/cloned/gh/LanHikari22/lan-obsidian-plugin
npm i
npm run dev
```

2025-09-10 Wk 37 Wed - 15:56 +03:00

Seems we need to restart obsidian to see our plugin show up.

```sh
killall obsidian
```

Hi again. Still don't see it... It should be called `obsidian-sample-plugin@1.0.0`

2025-09-10 Wk 37 Wed - 16:44 +03:00

Updated the `manifest.json` with my information and a new plugin name `lan-obsidian-plugin` or `Lan Obsidian Plugin` Updated `package.json` which has the project name also and is what shows up under `npm run dev`.

```sh
# in /home/lan/src/cloned/gh/LanHikari22/lan-obsidian-plugin
npm run dev
```

I might have to copy the manifest and main.js to `/home/lan/src/cloned/gh/LanHikari22/lan-setup-notes/.obsidian/plugins` or similar.

2025-09-10 Wk 37 Wed - 16:52 +03:00

Let's see if a soft link would do:

```sh
# in /home/lan/src/cloned/gh/LanHikari22/lan-setup-notes/.obsidian/plugins
ln -s ~/src/cloned/gh/LanHikari22/lan-obsidian-plugin lan-obsidian-plugin
```

Yup. On a restart it shows up:

![[Pasted image 20250910165345.png]]


It doesn't have many settings yet.

![[Pasted image 20250910165418.png]]

One thing to configure later on would be the categories for big notes.

2025-09-10 Wk 37 Wed - 17:05 +03:00

So `styles.css` should also go there. We should build styles to this if we need to.

(update)
And because of `tsconfig.json` having 
```json
  "include": [
    "**/*.ts"
  ]
```

We can move `main.ts` to `src/main.ts` and structure our ts code differently.

2025-09-10 Wk 37 Wed - 17:42 +03:00

We also had to change this:

```diff
- entryPoints: ["main.ts"],
+ entryPoints: ["src/main.ts"],
```

(/update)

2025-09-10 Wk 37 Wed - 17:19 +03:00

The main thing we want out of this currently is the ability to manipulate our notes and folders on a command. 

Let's start by automating spawning a note. We should be able to do `^P Lan spawn note` and it asks from a menu of available categories the note category, and then it asks for its name. It figures out the triplet ID on its own. Then it does what I'm about to do manually:

1. I wrote "Spawn SPW9" so that I can find that SPW9 and generate a random block identifier at that line later on. It's actually 0 rather than 9, but it doesn't matter. I didn't want this to pop up.
2. I created a new file for the new task.
3. I added frontmatter `parent` and `spawned_by`
4. I added `Parent: [[parent]]`.
5. I added `Spawned in [[this note#^SPW9]]` to generate the random block identifier id
6. I renamed the block identifier from `{rand}` to `spawn-task-{rand}`. This has to be done in both places.
7. I replaced SPW9 with the link to the new task note.
8. I added the new spawned task to the index in the parent.
9. I give the note a `status: todo` and I reflect that in the Index with `**Todo** [[new note]]` in the proper index category.

Spawn [[005 Create Spawn Note Command]] ^spawn-task-ebfb30

Yeah this got very involved and should not be something to do manually. It was much simpler, although still inconvenient before we introduced big notes.


 </file ./lan/tasks/2025/000 Note Repo Migration Sept 8/tasks/000 Create Obsidian extension for actions.md>



 <file ./lan/tasks/2025/000 Note Repo Migration Sept 8/tasks/002 Parse a single obsidian markdown file with pulldown cmark.md>

---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[001 How does obsidian-export parse markdown in postprocessors?]]"
context_type: task
status: done
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[001 How does obsidian-export parse markdown in postprocessors?]]

Spawned in: [[001 How does obsidian-export parse markdown in postprocessors?#^spawn-task-0d2c20|^spawn-task-0d2c20]]

# 1 Journal

2025-09-09 Wk 37 Tue - 15:25 +03:00

You can find the library [here](https://github.com/pulldown-cmark/pulldown-cmark/).

Let's build on the project we created in [[001 Parse through all notes and classify as core - peripheral - partial]]

```sh
# in /home/lan/src/cloned/gh/LanHikari22/lan-exp-scripts/scripts/2025/weekly/Wk37-000-obsidian-vault-migration-rs/migration_rs
cargo add pulldown-cmark
```

2025-09-09 Wk 37 Tue - 15:48 +03:00

Checked `tutorials/2025/topics/db/diesel.rs/getting-started/diesel_demo/src/bin/get_post.rs` for basic cli argument parsing

Checked `~/src/cloned/gh/LanHikari22/lan_rs_common/src/util/io.rs` for file IO

2025-09-09 Wk 37 Tue - 16:04 +03:00

We can now run

```sh
# in /home/lan/src/cloned/gh/LanHikari22/lan-exp-scripts/scripts/2025/weekly/Wk37-000-obsidian-vault-migration-rs/migration_rs
cargo run --bin ex000_parse_single_pulldown_cmark ~/src/cloned/gh/LanHikari22/lan-setup-notes/README.md
```

and get all the pulldown events printed for that speciifc markdown file.

We will see headings like this, which are of interest for [[001 Parse through all notes and classify as core - peripheral - partial|our classification task]]:

```
Event Start(Heading { level: H1, id: None, classes: [], attrs: [] })
Event Text(Borrowed("1 What is this?"))
Event End(Heading(H1))
```


 </file ./lan/tasks/2025/000 Note Repo Migration Sept 8/tasks/002 Parse a single obsidian markdown file with pulldown cmark.md>



 <file ./lan/tasks/2025/000 Note Repo Migration Sept 8/tasks/006 Regenerate cluster core note index heading.md>

---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[005 Create Spawn Note Command]]"
context_type: task
status: todo
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[005 Create Spawn Note Command]]

Spawned in: [[005 Create Spawn Note Command#^spawn-task-d5eb50|^spawn-task-d5eb50]]



 </file ./lan/tasks/2025/000 Note Repo Migration Sept 8/tasks/006 Regenerate cluster core note index heading.md>



 <file ./lan/tasks/2025/000 Note Repo Migration Sept 8/tasks/010 Apply markdown writeback on delta-trace vault.md>

---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[009 Impl and carry out cluster note migration]]"
context_type: task
status: done
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[009 Impl and carry out cluster note migration]] 

Spawned in: [[009 Impl and carry out cluster note migration#^spawn-task-e5af29|^spawn-task-e5af29]]

# 1 Journal

2025-09-23 Wk 39 Tue - 05:41 +03:00

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin app -- writeback ~/src/cloned/gh/deltatraced/delta-trace

# in /home/lan/src/cloned/gh/deltatraced/delta-trace
git status | wc -l

# out
240
```

Lots of things changed.

Spawn [[001 pulldown cmark to cmark escapes first obsidian tag on writeback]] ^spawn-issue-9b47f5

 </file ./lan/tasks/2025/000 Note Repo Migration Sept 8/tasks/010 Apply markdown writeback on delta-trace vault.md>



 <file ./lan/tasks/2025/000 Note Repo Migration Sept 8/tasks/009 Impl and carry out cluster note migration.md>

---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[000 Note Repo Migration Sept 8]]"
context_type: task
status: todo
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[000 Note Repo Migration Sept 8]] 

Spawned in: [[000 Note Repo Migration Sept 8#^spawn-task-3773c5|^spawn-task-3773c5]]

# 1 Journal

2025-09-17 Wk 38 Wed - 03:50 +03:00

2025-09-17 Wk 38 Wed - 21:23 +03:00

We're going to start by testing this on delta-trace. We write notes here for the migration, so modifying while capturing is awkward.

2025-09-17 Wk 38 Wed - 21:29 +03:00

Checked [gh LanHikari22/bn_repo_editor](https://github.com/LanHikari22/bn_repo_editor) for clap use for argparsing with [docs.rs clap](https://docs.rs/clap/latest/clap/).

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo add clap --features "cargo"
```

Spawn [[004 Writing events back to file with pulldown cmark]] ^spawn-howto-6ee267

2025-09-23 Wk 39 Tue - 04:16 +03:00

So we're able to convert from events back to markdown files, but this will likely introduce some minor noise, like line trims or spacing. Let's add a subcommand to just read events and write them back for all markdown files in the vault.

2025-09-23 Wk 39 Tue - 04:55 +03:00

Had to redo some minor work for some reason, but everything is commited now.

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin app -- writeback ~/src/cloned/gh/deltatraced/delta-trace

# out (error, relevant)
thread 'main' panicked at /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration/src/drivers.rs:67:10:
Failed to get working items
```

2025-09-23 Wk 39 Tue - 05:34 +03:00

Changed some `Option<T>` opaque errors into detailed errors per function.

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin app -- writeback ~/src/cloned/gh/deltatraced/delta-trace

# out (error, relevant)
thread 'main' panicked at /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration/src/drivers.rs:67:10:
Failed to get working items: FailedToGetCategoryFoldersAndPeripherals(NotAClusterCategoryFolderPath("/home/lan/src/cloned/gh/deltatraced/delta-trace/lan/templates/000 big note/side-notes"))
```

Oh I see! I still had that old template folder there when we started designing cluster notes. Let's delete it.

2025-09-23 Wk 39 Tue - 05:41 +03:00

Spawn [[010 Apply markdown writeback on delta-trace vault]] ^spawn-task-e5af29

2025-09-30 Wk 40 Tue - 08:40 +03:00

Spawn [[013 Change all delta-trace old format notes into note clusters without applying link fixing]] ^spawn-task-479e16


 </file ./lan/tasks/2025/000 Note Repo Migration Sept 8/tasks/009 Impl and carry out cluster note migration.md>



 <file ./lan/tasks/2025/000 Note Repo Migration Sept 8/tasks/007 Add some note name sanitization for spawn note.md>

---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[005 Create Spawn Note Command]]"
context_type: task
status: todo
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[005 Create Spawn Note Command]] 

Spawned in: [[005 Create Spawn Note Command#^spawn-task-b8adc5|^spawn-task-b8adc5]]

# 1 Journal

2025-09-15 Wk 38 Mon - 21:02 +03:00

Sanitize to not include `/` characters. Obsidian creates folder structures rather than notes when it sees those!

Also sanitize out any `[[` or `]]`.

 </file ./lan/tasks/2025/000 Note Repo Migration Sept 8/tasks/007 Add some note name sanitization for spawn note.md>



 <file ./lan/tasks/2025/000 Note Repo Migration Sept 8/tasks/005 Create Spawn Note Command.md>

---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[000 Create Obsidian extension for actions]]"
context_type: task
status: todo
---
#obsidian

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[000 Create Obsidian extension for actions]]

Spawned in [[000 Create Obsidian extension for actions#^spawn-task-ebfb30|^spawn-task-ebfb30]]

# 1 Journal

2025-09-10 Wk 37 Wed - 17:38 +03:00

We'll be testing this here, to see if we can easily spawn new commands.

Seems I have to do `^P Reload app without saving` to get changes to register.

2025-09-10 Wk 37 Wed - 18:02 +03:00

We added a currently empty command

```ts
this.addCommand({
	id: "spawn-note",
	name: "Spawn Note",
	editorCallback: spawn_note_command.run,
});
```

2025-09-10 Wk 37 Wed - 18:15 +03:00

The base information of interest to us is
- The current note, and its content
- The current cursor position in that note
- The folder the note belongs to, and possibly the folder that belongs to
- Ability to edit the content of notes in those folders
- Ability to add a category folder if it doesn't exist
- Ability to add a new note to a folder relative to our current folder

API information can be found at [gh obsidianmd/obsidian-api](https://github.com/obsidianmd/obsidian-api) and on the [obsidian developer docs](https://docs.obsidian.md/Home)

There is documentation on [replaceSelection and getSelection](https://docs.obsidian.md/Plugins/Editor/Editor#Replace+current+selection) that were used in the example.

We're interested in [Editor.getCursor](https://docs.obsidian.md/Reference/TypeScript+API/Editor/getCursor) and [Editor.getLine](https://docs.obsidian.md/Reference/TypeScript+API/Editor/getLine)

getCursor gives us an [EditorPosition](https://docs.obsidian.md/Reference/TypeScript+API/EditorPosition) whose line propery can be given to getLine to give us the current line the cursor is at.

We'll log with `console.log`, just do `Ctrl+Shift+I` and undock

![[Pasted image 20250910184323.png]]

2025-09-10 Wk 37 Wed - 19:38 +03:00

Looking at more API code found previously in scripts by LLMs:

There's [tp.app.vault.adapter](https://docs.obsidian.md/Reference/TypeScript+API/Vault/adapter) which gives us [DataAdapter](https://docs.obsidian.md/Reference/TypeScript+API/DataAdapter) that can be used to create directories, view directories, write content to files...

[read](https://docs.obsidian.md/Reference/TypeScript+API/DataAdapter/read) for example returns a promise, so we need to `await`.

2025-09-10 Wk 37 Wed - 20:15 +03:00

Here are some traces

```ts
import { Editor, MarkdownView } from 'obsidian';

export async function run(editor: Editor, view: MarkdownView) {
    console.log(`(Spawn)`)

    const opt_file = view.file;

    if (opt_file) {
        console.log(`file path: ${opt_file.path}`);
        console.log(`file name: ${opt_file.name}`);
        console.log(`file parent: ${opt_file.parent}`);
        console.log(`file stat: ${opt_file.stat}`);
        console.log(`file vault adapter name: ${opt_file.vault.adapter.getName()}`);

        console.log(`cache: ${view.app.metadataCache.getFileCache(opt_file)}`);

        const content = await opt_file.vault.read(opt_file);

        console.log(`content: ${content}`);

        const opt_other_file = opt_file.vault.getMarkdownFiles().find(f => f.basename === "000 Setting up time logging in Obsidian");
        if (opt_other_file) {
            console.log(`other_file path ${opt_other_file.path}`)
        }
    }


    const cursor = editor.getCursor();
    const current_line = editor.getLine(cursor.line);

    console.log(`cursor: ${cursor}`);
    console.log(`current_line: ${current_line}`);

    console.log(`doc: ${editor.getDoc()}`);

    console.log(`(/Spawn)\n\n`)
}
```

```
(Spawn)
file path: lan/tasks/2025/000 Note Repo Migration Sept 8/tasks/005 Create Spawn Note Command.md
file name: 005 Create Spawn Note Command.md
file parent: [object Object]
file stat: [object Object]
file vault adapter name: lan-setup-notes
cache: [object Object]
content: {this file's text}
other_file path lan/topics/tooling/obsidian/entries/2025/000 Setting up time logging in Obsidian.md
cursor: [object Object]
current_line: getCursor gives us an [EditorPosition](https://docs.obsidian.md/Reference/TypeScript+API/EditorPosition) whose line propery can be given to getLine to give us the current line the cursor is at.
doc: [object Object]
(/Spawn)
```

A lot of those say [object Object] so we should view them as JSON dictionaries.

2025-09-10 Wk 37 Wed - 20:29 +03:00

Using `JSON.stringify` on these objects causes an error `Converting circular structure to JSON`:

```ts
const opt_file = view.file;

if (opt_file) {
	console.log(`file parent: ${JSON.stringify(opt_file.parent)}`);
}
	
console.log(`doc: ${JSON.stringify(editor.getDoc())}`);
```

The cache gives us some information about the frontmatter properties as well as tags and other things like getting heading positions.

```ts
const opt_file = view.file;

if (opt_file) {
	console.log(`cache: ${JSON.stringify(view.app.metadataCache.getFileCache(opt_file))}`);
}
```

Some selective output:

```
tags":[{"position":{"start":{"line":5,"col":0,"offset":149},"end":{"line":5,"col":9,"offset":158}},"tag":"#obsidian"}]

"frontmatterLinks":[{"key":"parent","link":"000 Note Repo Migration Sept 8","original":"[[000 Note Repo Migration Sept 8]]","displayText":"000 Note Repo Migration Sept 8"},{"key":"spawned_by","link":"000 Create Obsidian extension for actions","original":"[[000 Create Obsidian extension for actions]]","displayText":"000 Create Obsidian extension for actions"}]
```

2025-09-10 Wk 37 Wed - 20:44 +03:00

For the cursor,

```ts
console.log(`cursor: ${JSON.stringify(cursor)}`);
```

```
cursor: {"line":47,"ch":192}

```

192 points to the index of the last character in the line. That line happens to be 193 characters long, and I took this trace with my cursor at the very end of the line.

We can get modification timestamps:

```ts
const opt_file = view.file;

if (opt_file) {
	console.log(`file stat: ${JSON.stringify(opt_file.stat)}`);
}
```

```
file stat: {"ctime":1757514159040,"mtime":1757526398374,"size":5764}
```

2025-09-10 Wk 37 Wed - 20:51 +03:00

via LLM suggestion we can use this to view the circular objects:

```ts
const opt_file = view.file;

if (opt_file) {
	console.dir(opt_file.parent, { depth: null });
}

console.dir(editor.getDoc(), { depth: null});
```

Those are big objects.

2025-09-10 Wk 37 Wed - 21:44 +03:00

Besides data and folder structure, we need to figure out how to prompt the user for input. We need them to write both the category (preferably to select it from a menu) and to write the name of the new note.

There's [SuggestModal](https://docs.obsidian.md/Reference/TypeScript+API/SuggestModal) and [FuzzySuggestModal](https://docs.obsidian.md/Reference/TypeScript+API/FuzzySuggestModal)

[obsidian forum post](https://forum.obsidian.md/t/prompt-user-input-api/36893/2) gives a link, but it's to migrated documentation and no longer points to where it should.  But it should be called `#accept-user-input`, which we can find in [obsidian docs Modals: Accept user input](https://docs.obsidian.md/Plugins/User+interface/Modals#Accept+user+input).

2025-09-10 Wk 37 Wed - 22:41 +03:00

User input is setup!

![[Pasted image 20250910224147.png]]

![[Pasted image 20250910224218.png]]

![[Pasted image 20250910224246.png]]


![[Pasted image 20250910224348.png]]

And finally the logic sets off once we have all user input!

2025-09-10 Wk 37 Wed - 21:01 +03:00

So when spawning a new small note, we should start by examining the folder structure. We need to be in a big note folder structure. Meaning that, the current file is the index file, or we are a small note in a valid category folder under a big note folder. We should give an error otherwise.

There is [Notice](https://docs.obsidian.md/Reference/TypeScript+API/Notice) for information messages. Don't see anything for other sorts of messages, so we can give an error on this (and the console).

2025-09-10 Wk 37 Wed - 23:04 +03:00

For a capability test, we need to be able to add content at the next line from the cursor on user invocation.

From [obsidian docs Insert text at cursor position](https://docs.obsidian.md/Plugins/Editor/Editor#Insert+text+at+cursor+position), 

We can see it's just a matter of replacing range at just the cursor position. For it to be a new line, maybe we can just add a new line character at the cursor position.

2025-09-10 Wk 37 Wed - 23:15 +03:00

[stackoverflow answer for random hex string](https://stackoverflow.com/a/1349426/6944447).

2025-09-11 Wk 37 Thu - 00:11 +03:00

(update)
I use `throw new Error("unimplemented");` for functions I've not written yet.

2025-09-11 Wk 37 Thu - 16:30 +03:00

To make it easier to use, added it as a vscode snippet in `/home/lan/.config/Code/User/snippets/typescript.json`

```json
"Unimplemented": {
	"prefix": "unimplemented",
	"body": [
		"throw new Error(\"unimplemented\");",
	],
	"description": "Unimplemented"
}
```

(/update)

2025-09-11 Wk 37 Thu - 01:33 +03:00

The implementation now should handle spawning a new small note and building a dual brindge, as well as all the frontmatter details. What remains after testing this is updating the index. 

2025-09-11 Wk 37 Thu - 01:40 +03:00

Now testing.

```
 Error: Could not find file for link [[000 Note Repo Migration Sept 8]]
 Error: Could not retrieve frontmatter note property parent for 005 Create Spawn Note Command.md
 Error: Could not get big note index file for 005 Create Spawn Note Command.md
```

It preserved the `[[ ]]`. Need to remove those.

2025-09-11 Wk 37 Thu - 01:51 +03:00

It fails to generate triplet IDs unless the context type folder exists. Need to create it if it doesn't.

2025-09-11 Wk 37 Thu - 01:58 +03:00

Just have to add `.md` to the generated file. It created it, but without extension. Other minor issue is that it said `spawn-inferences`, when it should be a small code `infer` for the block identifier.

2025-09-11 Wk 37 Thu - 02:07 +03:00

It works! All that remains is updating the index. We add a `status: todo` frontmatter property by default to spawned items unless they are an entry, an idea, or an inference. `status: {status}` this `{status}` needs to be captured in the index as `**{status}** {note}`. 

2025-09-11 Wk 37 Thu - 02:15 +03:00

Awesome! We can spawn much more freely now! Let's tackle the last item!

Spawn [[006 Regenerate cluster core note index heading]] ^spawn-task-d5eb50

2025-09-11 Wk 37 Thu - 02:36 +03:00

We're also able to spawn from the index file! One more thing, the shown categories should be singular. Not Entries, but Entry, for example. We're spawning just one note.

2025-09-11 Wk 37 Thu - 02:43 +03:00

Names changed! And made the string conversion general since there's many of them.

Spawn [[003 Rename github origin master branch to main]] ^spawn-howto-ea5401

2025-09-11 Wk 37 Thu - 17:38 +03:00

Input handling accepts `]]` and then results in issues.

Spawn [[007 Add some note name sanitization for spawn note]] ^spawn-task-b8adc5




 </file ./lan/tasks/2025/000 Note Repo Migration Sept 8/tasks/005 Create Spawn Note Command.md>



 <file ./lan/tasks/2025/000 Note Repo Migration Sept 8/tasks/001 Parse through all notes and classify as core - peripheral - partial.md>

---
parent: "[[000 Note Repo Migration Sept 8]]"
context_type: task
status: todo
---
Parent: [[000 Note Repo Migration Sept 8]]

# 1 Objective

Create a script that takes an obsidian vault path as input, and outputs classifications for every note encountered, including relevant metadata in a separate file. 

So it outputs a classification file, and a metadata file for additional data per file.

# 2 Journal

2025-09-09 Wk 37 Tue - 14:44 +03:00

Let's do this in rust and put it in [lan-exp-scripts](https://github.com/LanHikari22/lan-exp-scripts).

```sh
# in /home/lan/src/cloned/gh/LanHikari22/lan-exp-scripts/scripts/2025/weekly/Wk37-000-obsidian-vault-migration-rs/migration_rs
cargo init
```

2025-09-09 Wk 37 Tue - 14:50 +03:00

Spawn [[000 How should we parse markdown files in Rust?]] ^spawn-invst-38f502

2025-09-09 Wk 37 Tue - 16:07 +03:00

From [[002 Parse a single obsidian markdown file with pulldown cmark]],

We can now parse markdown events for any markdown file via the common mark format.

Spawn [[000 Break down lan-exp-scripts into an org]] ^spawn-entry-e65ac0

2025-09-09 Wk 37 Tue - 16:52 +03:00

Now that we have [deltaarchives](https://github.com/deltachives), let's put our project there instead.

2025-09-09 Wk 37 Tue - 17:39 +03:00

The project is in [gh deltachives/2025-Wk37-000-obsidian-migration](https://github.com/deltachives/2025-Wk37-000-obsidian-migration).

2025-09-09 Wk 37 Tue - 16:09 +03:00

2025-09-09 Wk 37 Tue - 18:15 +03:00

The new format index categories: `Entries, HowTos, Ideas, Inferences, Investigations, Issues, Tasks`

The old format heading categories: `Tasks, Issues, HowTos, Investigations, Ideas, Side Notes`

- big note
	- Exists within a folder of the same name
	- Contains an `# Index` `H1` heading with the new format index categories as `H2` subheadings.
- small note
	- Exists within a category of a big note folder
	- Has exactly one context. No old format entry headings, and no Index heading.
- hybrid note
	- This is the old format. It is a file that is not within a folder of the same name, and also it contains some H1 headings from the old format heading categories.
	- It contains at least one entry.
- small hybrid note
	- This is a note that may contain some of the old format heading categories, but contains no entries in them. This can changed into just a file, rather than a big note folder.

2025-09-09 Wk 37 Tue - 18:25 +03:00

Checked `rs_comm::io::glob_multiple_file_formats_in_path`. 

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo add itertools
```

2025-09-09 Wk 37 Tue - 18:47 +03:00

Spawn [[003 Make everything under lan_rs_common featured for very minimal includes]] ^spawn-task-d829aa

2025-09-09 Wk 37 Tue - 23:12 +03:00

Spawn [[001 Specifying arguments and returns in rust function docs]] ^spawn-howto-6ff1c8

2025-09-10 Wk 37 Wed - 01:01 +03:00

Spawn [[002 Copying folder structure but modifying some files in Rust]] ^spawn-howto-9a69d0

2025-09-13 Wk 37 Sat - 16:05 +03:00

We changed the names! Now it's one core note and many peripheral notes with context types all inside a note cluster!

Situation is also a bit more complicated because we have partial clusters now which we need to classify. Those are clusters whose core note still has old format records in it. Besides that, let's get to classifying!

2025-09-13 Wk 37 Sat - 16:22 +03:00

Spawn [[002 What's a directory entry from folder.read_dir in rust?]] ^spawn-invst-c00179

2025-09-13 Wk 37 Sat - 19:37 +03:00

Renaming `exNNN` binaries to `exptNNN` to denote they're experiments not example usage of the code.

2025-09-13 Wk 37 Sat - 19:44 +03:00

Spawn [[003 Investigating frontmatter to markdown event mappings with pulldown_cmark]] ^spawn-invst-3d5e66

2025-09-13 Wk 37 Sat - 20:00 +03:00

Let's parse `Rule H2 (Text [Softbreak] ...) /H2` at the beginning of the file for frontmatter.

2025-09-13 Wk 37 Sat - 20:17 +03:00

Checked [stackoverflow answer](https://stackoverflow.com/a/51429606/6944447) for `matches!` for enum variants

2025-09-13 Wk 37 Sat - 21:18 +03:00

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo add tap
```

Need this for `.pipe` to not have to make unnecessary intermediate variables or have unreadable compute chains.

2025-09-13 Wk 37 Sat - 21:34 +03:00

So for the intent of this migration, we're really only interested in modifying either individual markdown files at some arbitrary path, or cluster note folders at some path. When we make modifications, we inherit the path of either those two things. So let's just retrieve all the working items given a vault folder path. And we chose to do in-place modification, make sure to back up before running that.

2025-09-13 Wk 37 Sat - 23:19 +03:00

(update)
Let's make sure a valid vault path includes an `.obsidian/` folder and make sure to skip that folder.

2025-09-14 Wk 37 Sun - 02:05 +03:00

Even though we're not skipping `.obsidian/` there aren't any markdown files there so it isn't currently an issue. This might be a safe assumption.

(/update)

Implemented. Now let's create an experiment binary to show all working items of a vault.

2025-09-13 Wk 37 Sat - 23:40 +03:00

Spawn [[004 Debugging through migration expt003 errors]] ^spawn-invst-0f35dc

2025-09-14 Wk 37 Sun - 01:41 +03:00

Now we have all the working items of consideration! This on its own does a lot of classification for us. normal notes, cluster folders, core notes, category folders, and peripheral notes are all recognized via

```rust
pub enum WorkingPath {
    Note(NormalNoteFilePath),
    ClusterFolder{
        cluster_root_folder: ClusterRootFolderPath,
        core_note_file: CoreNoteFilePath,
        category_folders_with_peripheral_files: Vec<(ClusterCategoryFolderPath, Vec<PeripheralNoteFilePath>)>,
    }
}
```

What remains is to classify either normal notes or core notes further. normal notes can be clusters in one file as per the old format, and current core notes can be partial. that is, they still contain some entries within them as per the old format.

2025-09-14 Wk 37 Sun - 03:05 +03:00

As long as we can extract the old format records, we can tell that a note is of the old format if they contain them. If that happens to be a core file also, then we know there's partial core.

2025-09-14 Wk 37 Sun - 03:37 +03:00

We laid out the functions for the IO layer to do the migration. 

2025-09-14 Wk 37 Sun - 03:46 +03:00

Spawn [[005 Determining pulldown cmark events for old entries]] ^spawn-invst-7d671c

2025-09-14 Wk 37 Sun - 13:22 +03:00

For this, we will make a corresponding experiment `expt004` that lists the names, and number of events as well as existing headings + block identifiers for each entry in an old format note.

2025-09-14 Wk 37 Sun - 13:52 +03:00

Created `drivers.rs` to simplify the experiment code and keep it from getting repetitive. This has common driver patterns needed, like quickly setting up logging and requesting vault repo path from user via args.

2025-09-14 Wk 37 Sun - 15:01 +03:00

`expt004` is written to optionally take a note path in addition to the vault, to show more information on the objects extracted.

2025-09-15 Wk 38 Mon - 02:25 +03:00

I was getting an error on

```rust
pub fn get_note_old_format_entries<'a>(events: &Vec<Event<'a>>) -> Option<Vec<OldFormatEntry<'a>>> {
	// ...

	Some(old_format_entries)
//  ^^^^^^^^^^^^^^^^^^^^^^^^	
}
```

```
error[E0621]: explicit lifetime required in the type of `events`
   --> src/cluster_note.rs:676:5
    |
676 |     Some(old_format_entries)
    |     ^^^^^^^^^^^^^^^^^^^^^^^^ lifetime `'a` required
    |
help: add explicit lifetime `'a` to the type of `events`
    |
548 | pub fn get_note_old_format_entries<'a>(events: &'a Vec<Event<'a>>) -> Option<Vec<OldFormatEntry<'a>>> {
    |                                                 ++
```

which is fixed by making `events: &Vec` `events: &'a Vec` in the argument.

2025-09-15 Wk 38 Mon - 18:22 +03:00

We should look into some more robust error handling practices.

For example we learned from [gh Utagai/shi](https://github.com/Utagai/shi) about the [thiserror](https://docs.rs/thiserror/latest/thiserror/) crate for preventing dual locations of error string and error enum. We can use this.

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo add thiserror
```

2025-09-15 Wk 38 Mon - 19:12 +03:00

[thiserror](https://docs.rs/thiserror/latest/thiserror/) also recommends [gh dtolnay/anyhow](https://github.com/dtolnay/anyhow) for easy error propagation. 

2025-09-15 Wk 38 Mon - 19:16 +03:00

I ran into this pattern multiple times where I have a list `[A, B, C, D, B', E, F] ` and I want to turn it into groups by startpoint `B`, and endpoint `B'` to turn into `[[A], [B, C, D, B'], [E, F]]`

It happened with the `H1 Text /H1`  event handling, and now also with a string that may have many `[[link]]` s in it. Though for the link one, I've decided to just parse valid links at every char here. This would work because they cannot intersect, and would handle potential bracket imbalances.

2025-09-15 Wk 38 Mon - 20:34 +03:00

There's a lot of variance for how spawns are written. Let's try to fix this manually. 

Spawn [[008 Search for and fix old format Spawn strings for easier migration]] ^spawn-task-22c0e7

2025-09-16 Wk 38 Tue - 15:05 +03:00

Spawn [[006 Debugging through expt004 not terminating]] ^spawn-invst-59092e

2025-09-16 Wk 38 Tue - 22:48 +03:00

Spawn [[007 Debugging no old format records found in expt004]] ^spawn-invst-db4910

2025-09-17 Wk 38 Wed - 03:43 +03:00

Let's jump right to the migration itself now.

 </file ./lan/tasks/2025/000 Note Repo Migration Sept 8/tasks/001 Parse through all notes and classify as core - peripheral - partial.md>



 <file ./lan/tasks/2025/000 Note Repo Migration Sept 8/tasks/003 Make everything under lan_rs_common featured for very minimal includes.md>

---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[001 Parse through all notes and classify as core - peripheral - partial]]"
context_type: task
status: done
---
Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[001 Parse through all notes and classify as core - peripheral - partial]]

Spawned in: [[001 Parse through all notes and classify as core - peripheral - partial#^spawn-task-d829aa|^spawn-task-d829aa]]

# 1 Journal

2025-09-09 Wk 37 Tue - 19:29 +03:00

Spawn [[000 Include all crate features in build and vscode]] ^spawn-howto-358a72

2025-09-09 Wk 37 Tue - 23:46 +03:00

All of the util files now must be declared to be included by feature to keep downstream crate users light and only include what they need.

 </file ./lan/tasks/2025/000 Note Repo Migration Sept 8/tasks/003 Make everything under lan_rs_common featured for very minimal includes.md>



 <file ./lan/tasks/2025/000 Note Repo Migration Sept 8/tasks/012 Ensure spawned by line does not end with space after spawn command.md>

---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[011 Create integration tests for obsidian patch fixes for sept 8 migration]]"
context_type: task
status: done
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[011 Create integration tests for obsidian patch fixes for sept 8 migration]] 

Spawned in: [[011 Create integration tests for obsidian patch fixes for sept 8 migration#^spawn-task-99507e|^spawn-task-99507e]]

# 1 Related

[[000 Create Obsidian extension for actions]]

# 2 Journal

2025-09-30 Wk 40 Tue - 08:24 +03:00

```sh
# in /home/lan/src/cloned/gh/LanHikari22/lan-obsidian-plugin
git commit -m "update spec and rename bignote to note cluster"

# out
[main 303888d] update spec and rename bignote to note cluster
 Date: Tue Sep 30 08:26:06 2025 +0300
 5 files changed, 75 insertions(+), 60 deletions(-)
 rename src/{bignote.ts => notecluster.ts} (83%)
 rename src/{spawn_note_command.ts => spawn_peripheral_note_command.ts} (90%)
```

2025-09-30 Wk 40 Tue - 08:30 +03:00

```diff
# in spawn_peripheral_note_command.ts
# in function run_with_user_input
-`Spawned by: [[${spawner_file.basename}]] \n\n` +
+`Spawned by: [[${spawner_file.basename}]]\n\n` +
```

2025-09-30 Wk 40 Tue - 08:34 +03:00

```sh
# in /home/lan/src/cloned/gh/LanHikari22/lan-obsidian-plugin
git commit -m "remove unnecessary space at end of Spawned by"

# out
[main da9cbfd] remove unnecessary space at end of Spawned by
 1 file changed, 1 insertion(+), 1 deletion(-)
```

Now let's test it.

2025-09-30 Wk 40 Tue - 08:38 +03:00

```
npm run build
```

It should be working immediately for this vault since we temporarily have a local soft link for testing.

2025-09-30 Wk 40 Tue - 08:43 +03:00

Just have to make sure to restart obsidian also. We're no longer getting those spaces at the end of the line, so we're good!

 </file ./lan/tasks/2025/000 Note Repo Migration Sept 8/tasks/012 Ensure spawned by line does not end with space after spawn command.md>



 <file ./lan/tasks/2025/000 Note Repo Migration Sept 8/tasks/008 Search for and fix old format Spawn strings for easier migration.md>

---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[001 Parse through all notes and classify as core - peripheral - partial]]"
context_type: task
status: done
resolved: partial
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[001 Parse through all notes and classify as core - peripheral - partial]] 

Spawned in: [[001 Parse through all notes and classify as core - peripheral - partial#^spawn-task-22c0e7|^spawn-task-22c0e7]]

# 1 Journal

2025-09-15 Wk 38 Mon - 20:35 +03:00

Let's start with `001 Turn EWRAM and ROM Structs into C Structs and embed into type for gdb memory manipulation` in dism-exe-notes. This is one of the biggest files, and also earliest, so the notation varied wildly there.

2025-09-16 Wk 38 Tue - 06:01 +03:00

Some thing we wrote and moved here from a comment:

```
    // Our latest Spawned format is
    // (0) "From {block_identifier} in {spawner_note}"
    // where {block_identifier} breaks into "spawn-{category}-{randhexstr6}"

    // But we also used others before it
    // (1) "From {spawner_note}."
    // (2) "From {block_identifier}."

    // Some are actually broken. For example, we manually would add "spawn-{category}-" to the 6-digit random hex
    // identifier created by obsidian. But then we would forget to add it there. But you should still be able to infer this.
    // (3) "From {broken_block_identifier}" in {spawner_note}.
    // Of course, Anticipate {broken_block_identifier} in (2) too.

    // Similarly for Spawn, the format is
    // (0) Spawn {spawned_note} {block_identifier}

    // But before that we would add titles, or even just put it in the same line as other text.

    // It might be best to assume latest format and fix the rest manually.
```



 </file ./lan/tasks/2025/000 Note Repo Migration Sept 8/tasks/008 Search for and fix old format Spawn strings for easier migration.md>



 <file ./lan/tasks/2025/000 Note Repo Migration Sept 8/tasks/004 Parse parent frontmatter property for time log summaries.md>

---
parent: "[[000 Note Repo Migration Sept 8]]"
context_type: task
status: done
---

Parent: [[000 Note Repo Migration Sept 8]]

# 1 Objective

Once we introduced big note folder structures, we no longer get subitems in our summaries, everything becomes a main item. 

All time logs within a file that has a `parent` frontmatter property, should be a child of that parent note, even if it is subheadings within a file. 

# 2 Journal

2025-09-10 Wk 37 Wed - 05:23 +03:00

We now are able to scan notes for frontmatter with `parent` property and include those as subitems to the superitem of the parent.

One thing we notice is that the timestamps of the simple time logger are not the same timestamp used here, so some items from next day end up in the same day, at least within midnight. Something to look into, but not a current priority.

Noting issue in [[000 Setting up time logging in Obsidian]].


 </file ./lan/tasks/2025/000 Note Repo Migration Sept 8/tasks/004 Parse parent frontmatter property for time log summaries.md>



 <file ./lan/tasks/2025/000 Note Repo Migration Sept 8/000 Note Repo Migration Sept 8.md>

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

2025-09-22 Wk 39 Mon - 23:19 +03:00

Spawn [[001 Resources encountered during note repo migration sept 8]] ^spawn-entry-60fe40

2025-09-23 Wk 39 Tue - 00:00 +03:00

Spawn [[001 Some ideas encountered during note repo migration sept 8]] ^spawn-idea-57ecd9

2025-09-23 Wk 39 Tue - 09:55 +03:00

Turns out if we have a `main.rs`, it's included in the `cargo run --bin` options as the name of the crate. In our case, `migration_rs`. But we created an `app` under bin. Let's remove `main.rs` since we can use `--bin app`. 

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

 </file ./lan/tasks/2025/000 Note Repo Migration Sept 8/000 Note Repo Migration Sept 8.md>



 <file ./lan/tasks/2025/000 Note Repo Migration Sept 8/entries/000 Break down lan-exp-scripts into an org.md>

---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[001 Parse through all notes and classify as core - peripheral - partial]]"
context_type: entry
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[001 Parse through all notes and classify as core - peripheral - partial]]

Spawned in: [[001 Parse through all notes and classify as core - peripheral - partial#^spawn-entry-e65ac0|^spawn-entry-e65ac0]]

# 1 Journal

2025-09-09 Wk 37 Tue - 16:25 +03:00

Our work right now is all about respecting file atomicity of context. By this, I mean that each file has exactly one context, and it links or indexes other contexts otherwise. 

We created [lan-exp-scripts](https://github.com/LanHikari22/lan-exp-scripts) with the hope to provide public storage for all scripts and files associated with our notes, but it violates the atomicity principle. This repository is an "everything repository", which makes it not very usable, blends all git histories of everything we ever do together, and hinders usability.

The design decision behind this was because it was visualized to just be a place to put all scripts in without overrunning our repository count with many little script repositories. For this, we will instead create a dedicated org, `deltalab` where we can create as many experimental and small repositories as we want. This can also keep `deltatraced` from getting diluted with many repositories.

We followed a methodology of weekly streams where we classify the project by week. We can still do this. Even tutorial reproductions end up in `lan-exp-scripts`, as well as templates.

2025-09-09 Wk 37 Tue - 16:49 +03:00

Ok! `deltalab` was taken, but we created [deltachives](https://github.com/deltachives)!

2025-09-09 Wk 37 Tue - 17:04 +03:00

With this, we restore the practice all git repositories we have are very focused and have a single purpose, rather than be treated as a catch-all archive. Let an org do that!

 </file ./lan/tasks/2025/000 Note Repo Migration Sept 8/entries/000 Break down lan-exp-scripts into an org.md>



 <file ./lan/tasks/2025/000 Note Repo Migration Sept 8/entries/001 Resources encountered during note repo migration sept 8.md>

---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[000 Note Repo Migration Sept 8]]"
context_type: entry
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[000 Note Repo Migration Sept 8]] 

Spawned in: [[000 Note Repo Migration Sept 8#^spawn-entry-60fe40|^spawn-entry-60fe40]]

# 1 Journal

During investigating [[008 How does obsidian-export export pulldown-cmark back to markdown?]],

(1)

2025-09-22 Wk 39 Mon - 23:19 +03:00

We found out that they use [docs.rs snafu](https://docs.rs/snafu/latest/snafu/) for error handling. Example usage from obsidian-markdown: [1](https://github.com/zoni/obsidian-export/blob/87cb4ceb0ae48b391da4b02b94c6a909b1eb438d/src/lib.rs#L142).

(2)

2025-09-22 Wk 39 Mon - 23:38 +03:00

In [here](https://github.com/zoni/obsidian-export/blob/87cb4ceb0ae48b391da4b02b94c6a909b1eb438d/src/lib.rs#L250), obsidian-export chooses to explicitly use a formatter's `.debug_struct`, `.field`, and `.finish` to specify how to fully display a struct.

 </file ./lan/tasks/2025/000 Note Repo Migration Sept 8/entries/001 Resources encountered during note repo migration sept 8.md>



 <file ./lan/tasks/2025/000 Note Repo Migration Sept 8/issues/001 pulldown cmark to cmark escapes first obsidian tag on writeback.md>

---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[010 Apply markdown writeback on delta-trace vault]]"
context_type: issue
status: done
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

2025-09-30 Wk 40 Tue - 08:09 +03:00

writeback now does not change delta-trace vault



 </file ./lan/tasks/2025/000 Note Repo Migration Sept 8/issues/001 pulldown cmark to cmark escapes first obsidian tag on writeback.md>



 <file ./lan/tasks/2025/000 Note Repo Migration Sept 8/issues/000 Precommit errors prior to expt004 commit.md>

---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[000 Note Repo Migration Sept 8]]"
context_type: issue
status: done
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[000 Note Repo Migration Sept 8]] 

Spawned in: [[000 Note Repo Migration Sept 8#^spawn-issue-7a20bd|^spawn-issue-7a20bd]]

# 1 Journal

2025-09-17 Wk 38 Wed - 03:52 +03:00

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
git add .
pre-commit run
```

2025-09-17 Wk 38 Wed - 03:57 +03:00

```rust
error: invalid character ' ' in crate name: `expt000_parse_single_pulldown_cmark copy`                                                                                                        
  |                                                                                            
  = help: you can either pass `--crate-name` on the command line or add `#![crate_name = "…"]` to set the crate name
                                               
error: could not compile `migration_rs` (bin "expt000_parse_single_pulldown_cmark copy" test) due to 1 previous error

```

Oops it had a space.

```rust
error: called `is_some()` after searching an `Iterator` with `find`                                                                                                                           
  --> src/cluster_note.rs:75:10     
   |
75 |           .find(|heading| *heading == CONTEXT_TYPE_HEADINGS[context_type_id])
   |  __________^
76 | |         .is_some()                                                                      
   | |__________________^ help: consider using: `any(|heading| heading == CONTEXT_TYPE_HEADINGS[context_type_id])`
   |
   = help: for further information visit https://rust-lang.github.io/rust-clippy/master/index.html#search_is_some
   = note: `-D clippy::search-is-some` implied by `-D warnings`
   = help: to override `-D warnings` add `#[allow(clippy::search_is_some)]`
```

That's definitely simpler

2025-09-17 Wk 38 Wed - 03:59 +03:00

```rust
error: writing `&PathBuf` instead of `&Path` involves a new object where a slice will do
  --> src/cluster_note.rs:79:49
   |                                                                                                                                                                                          
79 | pub fn file_exists_in_folder_of_same_name(path: &PathBuf) -> bool {
   |                                                 ^^^^^^^^ help: change this to: `&Path`
   |                                                                                           
   = help: for further information visit https://rust-lang.github.io/rust-clippy/master/index.html#ptr_arg
   = note: `-D clippy::ptr-arg` implied by `-D warnings`
   = help: to override `-D warnings` add `#[allow(clippy::ptr_arg)]`
```

So `Path` is like a slice type for `PathBuf`! I guess with it being a slice instead, many different data structures could be used. Just like you don't use `&Vec<T>` when you can get away with `&[T]` because you could use slicing operations as well as arrays and vectors would be usable with that function.

2025-09-17 Wk 38 Wed - 04:07 +03:00

```rust
error: match can be simplified with `.unwrap_or_default()`
  --> src/cluster_note.rs:92:5
   |
92 | /     match inner_fn() {
93 | |         Some(res) => res,
94 | |         None => false,
95 | |     }
   | |_____^ help: ascribe the type bool and replace your expression with: `inner_fn().unwrap_or_default()`
   |
   = help: for further information visit https://rust-lang.github.io/rust-clippy/master/index.html#manual_unwrap_or_default
   = note: `-D clippy::manual-unwrap-or-default` implied by `-D warnings`
   = help: to override `-D warnings` add `#[allow(clippy::manual_unwrap_or_default)]`
```

Okay. `bool::default()` does give `false`. 

```rust
inner_fn().unwrap_or_default()
```

So we either get the result or the default!

2025-09-17 Wk 38 Wed - 04:21 +03:00

```rust
error: called `filter(..).next()` on an `Iterator`. This is more succinctly expressed by calling `.find(..)` instead
```

```rust
error: `filter(..).map(..)` can be simplified as `filter_map(..)`
   --> src/cluster_note.rs:435:10
    |
435 |           .filter(|opt_path| opt_path.is_some())
    |  __________^
436 | |         .map(|opt_path| opt_path.expect("Nones should already be filtered out"))
    | |________________________________________________________________________________^ help: try: `filter_map(|opt_path| opt_path)`
    |
    = help: for further information visit https://rust-lang.github.io/rust-clippy/master/index.html#manual_filter_map
    = note: `-D clippy::manual-filter-map` implied by `-D warnings`
    = help: to override `-D warnings` add `#[allow(clippy::manual_filter_map)]`
```

Really? `filter_map(|opt_path| opt_path)`?

It works! It somehow knows to just take out the `Nones` that way!

That's way better than what I did where I explained why I did `.expect`!

2025-09-17 Wk 38 Wed - 04:31 +03:00

```rust
error: returning the result of a `let` binding from a block
   --> src/cluster_note.rs:498:5
    |
493 | /     let all_are_numbers = s
494 | |         .split(".")
495 | |         .map(|token| usize::from_str_radix(token, 10).ok())
496 | |         .all(|token| token.is_some());
    | |______________________________________- unnecessary `let` binding
497 |
498 |       all_are_numbers
    |       ^^^^^^^^^^^^^^^
    |
    = help: for further information visit https://rust-lang.github.io/rust-clippy/master/index.html#let_and_return
    = note: `-D clippy::let-and-return` implied by `-D warnings`
    = help: to override `-D warnings` add `#[allow(clippy::let_and_return)]`
help: return the expression directly
    |
493 ~     
494 |
495 ~     s
496 +         .split(".")
497 +         .map(|token| usize::from_str_radix(token, 10).ok())
498 +         .all(|token| token.is_some())
```

Eeeh. I guess it is unnecessary, but I usually create my functions as a collection of immutable let bindings. But it makes sense that the name of the last one should simply be denoted by the function name. Doing it that way though allows for easy tracing. But we can always turn it into a let binding if we want to trace, so not a big deal.

It seems happy though with the pattern here:

```rust
pub fn note_link_to_path(vault: &[WorkingPath], note_link: &str) -> Option<PathBuf> {
    let path = vault
        .iter()
        .map(|item| match item {
			// ...
        })
        .filter_map(|opt_path| opt_path)
        .next()?;

    Some(path)
}
```

since we do a little processing in the end. I do this because of the tracing point made earlier, and to document the final happy path value expression.

Still let's make it more concise as

```rust
pub fn note_link_to_path(vault: &[WorkingPath], note_link: &str) -> Option<PathBuf> {
    vault
        .iter()
        .map(|item| match item {
			// ...
        })
        .filter_map(|opt_path| opt_path)
        .next()
}
```

as it is consistent with the previous change and it's a bit redundant to capture it as an Option again.


2025-09-17 Wk 38 Wed - 04:43 +03:00

```rust
error: use of `filter_map` with an identity function
   --> src/cluster_note.rs:433:10
    |
433 |         .filter_map(|opt_path| opt_path)
    |          ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ help: try: `flatten()`
    |
    = help: for further information visit https://rust-lang.github.io/rust-clippy/master/index.html#filter_map_identity
    = note: `-D clippy::filter-map-identity` implied by `-D warnings`
    = help: to override `-D warnings` add `#[allow(clippy::filter_map_identity)]`
```

Could've suggested that first...

```rust
error: this expression creates a reference which is immediately dereferenced by the compiler
   --> src/cluster_note.rs:117:32
    |
117 |     if !is_cluster_root_folder(&folder.parent()?)? {
    |                                ^^^^^^^^^^^^^^^^^ help: change this to: `folder.parent()?`
    |
    = help: for further information visit https://rust-lang.github.io/rust-clippy/master/index.html#needless_borrow
    = note: `-D clippy::needless-borrow` implied by `-D warnings`
    = help: to override `-D warnings` add `#[allow(clippy::needless_borrow)]`
```

Oops, `.parent()` returns an `Option<&Path>` so with `?` that's already a `&Path`.

2025-09-17 Wk 38 Wed - 12:19 +03:00

```
error: called `map(..).flatten()` on `Iterator`
   --> src/cluster_note.rs:402:10
    |
402 |           .map(|item| match item {
    |  __________^
403 | |             WorkingPath::Note(normal_note_file_path) => {
404 | |                 if normal_note_file_path.path.ends_with(note_link) {
405 | |                     Some(normal_note_file_path.path.clone())
...   |
432 | |         })
433 | |         .flatten()
    | |__________________^
    |
    = help: for further information visit https://rust-lang.github.io/rust-clippy/master/index.html#map_flatten
    = note: `-D clippy::map-flatten` implied by `-D warnings`
    = help: to override `-D warnings` add `#[allow(clippy::map_flatten)]`

```

2025-09-17 Wk 38 Wed - 12:22 +03:00

```
error: using `clone` on type `HeadingLevel` which implements the `Copy` trait
   --> src/common.rs:302:25
    |
302 |                         heading_level.clone(),
    |                         ^^^^^^^^^^^^^^^^^^^^^ help: try dereferencing it: `*heading_level`
    |
    = help: for further information visit https://rust-lang.github.io/rust-clippy/master/index.html#clone_on_copy
```

2025-09-17 Wk 38 Wed - 12:38 +03:00

Finally done

 </file ./lan/tasks/2025/000 Note Repo Migration Sept 8/issues/000 Precommit errors prior to expt004 commit.md>



 <file ./lan/tasks/2025/000 Note Repo Migration Sept 8/issues/002 vscode rust analyzer does not work in integration test directory.md>

---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[001 pulldown cmark to cmark escapes first obsidian tag on writeback]]"
context_type: issue
status: done
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[001 pulldown cmark to cmark escapes first obsidian tag on writeback]] 

Spawned in: [[001 pulldown cmark to cmark escapes first obsidian tag on writeback#^spawn-issue-07b5a7|^spawn-issue-07b5a7]]

# 1 Journal


2025-09-24 Wk 39 Wed - 03:26 +03:00

It just says the file isn't included anywhere, so it won't analyze it. 

[gh rust-lang/rust-analyzer #2034](https://github.com/rust-lang/rust-analyzer/issues/2034)

[post rust-lang](https://users.rust-lang.org/t/vscode-rust-analyzer-doesnt-function-in-tests-folder/94790)

2025-09-24 Wk 39 Wed - 03:30 +03:00

Okay the issue for me was just that I accidentally had `tests/` inside `src/` when it should be outside since integration tests are not part of the crate.

 </file ./lan/tasks/2025/000 Note Repo Migration Sept 8/issues/002 vscode rust analyzer does not work in integration test directory.md>



 <file ./lan/tasks/2025/000 Note Repo Migration Sept 8/ideas/000 Can create markdown guides with mdbook.md>

---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[004 Writing events back to file with pulldown cmark]]"
context_type: idea
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[004 Writing events back to file with pulldown cmark]] 

Spawned in: [[004 Writing events back to file with pulldown cmark#^spawn-idea-e6399b|^spawn-idea-e6399b]]

# 1 Journal

2025-09-22 Wk 39 Mon - 22:59 +03:00

pulldown-cmark offers an example in their github [here](https://github.com/pulldown-cmark/pulldown-cmark/tree/master/guide) with the corresponding guide [here](https://pulldown-cmark.github.io/pulldown-cmark/).


 </file ./lan/tasks/2025/000 Note Repo Migration Sept 8/ideas/000 Can create markdown guides with mdbook.md>



 <file ./lan/tasks/2025/000 Note Repo Migration Sept 8/ideas/002 Can impl a cmark to obsidian markdown crate.md>

---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[001 pulldown cmark to cmark escapes first obsidian tag on writeback]]"
context_type: idea
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[001 pulldown cmark to cmark escapes first obsidian tag on writeback]] 

Spawned in: [[001 pulldown cmark to cmark escapes first obsidian tag on writeback#^spawn-idea-51f0eb|^spawn-idea-51f0eb]]

# 1 Journal

2025-09-23 Wk 39 Tue - 09:28 +03:00

We're doing a comparison patch in an ad-hoc way, but it's possible to dig further into this for a proper treatment of obsidian markdown.

Also, some problems start in treating obsidian markdown as common markdown in [gh pulldown-cmark/pulldown-cmark](https://github.com/pulldown-cmark/pulldown-cmark/).

frontmatter is not supported, neither are obsidian links.  Those issues are propagated through [gh Byron/pulldown-cmark-to-cmark](https://github.com/Byron/pulldown-cmark-to-cmark) when trying to render back to obsidian markdown.

The spawner context for this idea covers some of the differences and ad-hoc patches attempted.

 </file ./lan/tasks/2025/000 Note Repo Migration Sept 8/ideas/002 Can impl a cmark to obsidian markdown crate.md>



 <file ./lan/tasks/2025/000 Note Repo Migration Sept 8/ideas/001 Some ideas encountered during note repo migration sept 8.md>

---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[000 Note Repo Migration Sept 8]]"
context_type: idea
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[000 Note Repo Migration Sept 8]] 

Spawned in: [[000 Note Repo Migration Sept 8#^spawn-idea-57ecd9|^spawn-idea-57ecd9]]

# 1 Journal

(1)

2025-09-23 Wk 39 Tue - 00:01 +03:00

Since every peripheral note now has a random 6 hex digit identifier via spawns, it could be possible to always identify them. For example, if they're arbitrarily renamed or relocated in the future but the id remains in tact, we could search version control for that id and identify their old name and location for link fixing.

Although location should not be a big issue, since we refer to them by their filename, but if they are being referred to from the outside by another vault it might be an issue. I do think we should eventually look into working with networked notes, in a similar fashion. For example, `[[NNN my networked note#with some heading|and some title@vault-url]]`

Although to support getting all notes from a networked vault, maybe `[[vault-url:NNN my networked note#with some heading|and some title]]`



 </file ./lan/tasks/2025/000 Note Repo Migration Sept 8/ideas/001 Some ideas encountered during note repo migration sept 8.md>



 <file ./lan/tasks/2025/000 Note Repo Migration Sept 8/investigations/008 How does obsidian-export export pulldown-cmark back to markdown?.md>

---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[004 Writing events back to file with pulldown cmark]]"
context_type: investigation
status: done
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[004 Writing events back to file with pulldown cmark]] 

Spawned in: [[004 Writing events back to file with pulldown cmark#^spawn-invst-ebcfc2|^spawn-invst-ebcfc2]]

# 1 Journal

2025-09-22 Wk 39 Mon - 23:44 +03:00

[run](https://github.com/zoni/obsidian-export/blob/87cb4ceb0ae48b391da4b02b94c6a909b1eb438d/src/lib.rs#L356) $\to$ [export_note](https://github.com/zoni/obsidian-export/blob/87cb4ceb0ae48b391da4b02b94c6a909b1eb438d/src/lib.rs#L418) $\to$ [parse_and_export_obsidian_note](https://github.com/zoni/obsidian-export/blob/87cb4ceb0ae48b391da4b02b94c6a909b1eb438d/src/lib.rs#L441) $\to$ [render_mdevents_to_mdtext](https://github.com/zoni/obsidian-export/blob/87cb4ceb0ae48b391da4b02b94c6a909b1eb438d/src/lib.rs#L830)

This uses [pulldown_cmark_to_cmark](https://docs.rs/pulldown-cmark-to-cmark/latest/pulldown_cmark_to_cmark/) ([lib.rs](https://lib.rs/crates/pulldown-cmark-to-cmark) [gh](https://github.com/byron/pulldown-cmark-to-cmark)).

2025-09-22 Wk 39 Mon - 23:55 +03:00

Some resources are also recorded in [[001 Resources encountered during note repo migration sept 8]]

 </file ./lan/tasks/2025/000 Note Repo Migration Sept 8/investigations/008 How does obsidian-export export pulldown-cmark back to markdown?.md>



 <file ./lan/tasks/2025/000 Note Repo Migration Sept 8/investigations/007 Debugging no old format records found in expt004.md>

---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[001 Parse through all notes and classify as core - peripheral - partial]]"
context_type: investigation
status: done
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[001 Parse through all notes and classify as core - peripheral - partial]] 

Spawned in: [[001 Parse through all notes and classify as core - peripheral - partial#^spawn-invst-db4910|^spawn-invst-db4910]]

# 1 Journal

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt004_old_format_record_list ~/src/cloned/gh/LanHikari22/lan-setup-notes

# out (relevent)
[nothing]
```

There should be many.

2025-09-16 Wk 38 Tue - 22:55 +03:00

Maybe let's rename form `SubH2` to just `content`. This breaks that assumption:

```
H1("1 What is this?"), SubH2([Start(Paragraph), Text(Borrowed("To protect against broken links, anywhere notes are referenced externally will be recorded here.")), End(Paragraph)]), H1("2 Journal"),
```

If it does not handle H1, or H2, it just assumes the third category and starts collecting.

So we are able to see grouped events but not relevant grouped events:

```rust
log::trace!("{relevant_grouped_events:?}");
```

These show nothing.

2025-09-16 Wk 38 Tue - 23:52 +03:00

Was just a matter of trimming in

```rust
!OLD_FORMAT_HEADINGS.contains(&strip_autonumbered_sections(&heading1).trim())
```

and now we have relevant grouped events.

2025-09-16 Wk 38 Tue - 23:55 +03:00

Now this shows nothing

```rust
log::trace!("{old_format_entries:?}");
```

2025-09-17 Wk 38 Wed - 00:15 +03:00

Turned the Option errors into Result and temporarily replaced `?` with unwrap to inspect.

```
thread 'main' panicked at src/cluster_note.rs:652:89:
called `Result::unwrap()` on an `Err` value: InvalidEntryType(InvalidType("4 HowTos"))
```

Okay, we just haven't stripped the heading numbers.

```
thread 'main' panicked at src/cluster_note.rs:652:89:
called `Result::unwrap()` on an `Err` value: InvalidEntryType(InvalidType(" HowTos"))
```

And also didn't trim it!

2025-09-17 Wk 38 Wed - 00:32 +03:00

Now `expt004` gives us old format event information!

`000 Create script to analyze expedition 33 golgra fight and count turns.md`  though says `5.1` has no spawn metadata and no linkables.

There aren't linkables, but it does have spawn.

2025-09-17 Wk 38 Wed - 00:55 +03:00

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt004_old_format_record_list ~/src/cloned/gh/LanHikari22/lan-setup-notes ~/src/cloned/gh/LanHikari22/lan-setup-notes/lan/topics/gaming/tasks/2025/000\ Create\ script\ to\ analyze\ expedition\ 33\ golgra\ fight\ and\ count\ turns.md | less
```

The event we're interested in for `spawned` is 

```
ObsidianLinkItem { links: [], event: Text(Boxed("From [[#^spawn-howto-ba135a]] in [[#3.1 Create Script to press keys at exact intervals]]")) }
```

But yet it says there are no links in it... even though it created the object.

(update)
There's also a weird event

```
ObsidianLinkItem { links: [], event: Text(Inlined(InlineStr { inner: [91, 120, 93, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0], len: 3 })) }
```

2025-09-17 Wk 38 Wed - 01:52 +03:00

You also can't find it when you just look through the events with `expt000`. 

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt000_parse_single_pulldown_cmark ~/src/cloned/gh/LanHikari22/lan-setup-notes/lan/topics/gaming/tasks/2025/000\ Create\ script\ to\ analyze\ expedition\ 33\ golgra\ fight\ and\ count\ turns.md | less
```

2025-09-17 Wk 38 Wed - 02:47 +03:00

```sh
python3 -c "import checkpipe as pipe; l = [91, 120, 93, 0] | pipe.OfIter[int].map(lambda n: chr(n)) | pipe.OfIter[str].to_list(); print(''.join(l))"

# out
[x]
```

There are multiple events like that:

```
Event Text(Boxed("[x]"))
```

We're dealing with `&Event` instead of `Event`...

2025-09-17 Wk 38 Wed - 02:56 +03:00

We were able to reproduce the behavior in `expt000` by using

```rust
#[derive(Debug)]
pub struct Test<'a> {
	event: Event<'a>
}

println!("Event {:?}", Test {event: event.clone()});
```

Instead of

```rust
println!("Event {event:?}");
```

(/update)

2025-09-17 Wk 38 Wed - 01:08 +03:00

`parse_multiple_obsidian_links` adopts the strategy of trying to parse for a valid token at each character. But the problem is it expects a single token, and not just the beginning of a character stream.

Let's properly tokenize.

2025-09-17 Wk 38 Wed - 01:30 +03:00

We're tokenizing, and now extract has proper error type. Let's check what we get.

```
thread 'main' panicked at src/bin/expt004_old_format_record_list.rs:22:22:
Failed to extract obsidian links: LinkExtractError(NoBracketsFound("8710732505532484849327911032118105115117971083210511011211711644321121141011151153211511110910132107101121115"))
```

Oh no. 

```rust
let stripped = s
	.as_bytes()
	[..end_idx]
	.iter()
	.join("");
```

This did not go as planned. It's just turning the numbers themselves into strings!

2025-09-17 Wk 38 Wed - 01:39 +03:00

Correcting to this.

```rust
let stripped = s
	.chars()
	.take(end_idx + "]]".len())
	.join("");
```

We want to also include the `]]`.

```
thread 'main' panicked at src/bin/expt004_old_format_record_list.rs:22:22:
Failed to extract obsidian links: LinkExtractError(NoBracketsFound("Wk 27 001 On visual input, press some keys]]"))
```

And don't forget to add the `[[` back...

```
thread 'main' panicked at src/bin/expt004_old_format_record_list.rs:22:22:
Failed to extract obsidian links: LinkExtractError(MustHaveZeroOrOneHash(2))
```

2025-09-17 Wk 38 Wed - 01:46 +03:00

```
thread 'main' panicked at src/bin/expt004_old_format_record_list.rs:22:22:
Failed to extract obsidian links: LinkExtractError(MustHaveZeroOrOneHash(2, "#5.1 Listen for key events in Linux in Python3[[#5.1 Listen for key events in Linux in Python3]]"))
```

A trace shows that we're tokenizing fine:

```
[2025-09-17 01:48:28 TRACE src/common.rs:587] tokens: ["[[#5.1 Listen for key events in Linux in Python3]]"]
```

So the `ObsidianLink` parser is failing.

```rust
let remaining_s = s.replace("[[", "").replace("]]", s);
```

Yeah definitely don't replace `"]]"` with `s`. Bad typo.

2025-09-17 Wk 38 Wed - 01:53 +03:00

There's still the issue with events that have 0 links appearing like

```
ObsidianLinkItem { links: [], event: Text(Borrowed("2025-08-27 Wk 35 Wed - 18:10")) }
```

(update)

We can return an empty vec in that case.

<details>
<summary>deprecated</summary>

2025-09-17 Wk 38 Wed - 01:59 +03:00

Deprecated the following, since we can just return an empty vec.

We added a new error to `extract_obsidian_md_links`,

```rust
if links.is_empty() {
	return Err(ExtractOBsidianMdLinksError::NoLinksFound)
}
```

This is a failure that `expt004` should be able to handle.
</details>

(/update)

2025-09-17 Wk 38 Wed - 02:01 +03:00

Spawn metadata shows up now!

2025-09-17 Wk 38 Wed - 03:10 +03:00

We need to check that we're parsing linkables correctly.

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt004_old_format_record_list ~/src/cloned/gh/LanHikari22/lan-setup-notes | less

# out (relevant)
path "/home/lan/src/cloned/gh/LanHikari22/lan-setup-notes/lan/topics/tooling/obsidian/tasks/2025/004 Fix obsidian export to support internal links.md" has 24 records
[2025-09-17 03:12:04 TRACE src/bin/expt004_old_format_record_list.rs:33] record "2.1 Capture details on the broken links problem" of Task has 322 events.
[2025-09-17 03:12:04 TRACE src/bin/expt004_old_format_record_list.rs:34]        It has 7 linkables, 14 links, and 1 spawn items.
```

That has 7 headings, so it correctly has 7 linkables. No block identifiers.

2025-09-17 Wk 38 Wed - 03:17 +03:00

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt004_old_format_record_list ~/src/cloned/gh/LanHikari22/lan-setup-notes ~/src/cloned/gh/LanHikari22/lan-setup-notes/lan/topics/tooling/obsidian/tasks/2025/004\ Fix\ obsidian\ export\ to\ support\ internal\ links.md | less

# out (relevant)
[2025-09-17 03:17:41 TRACE src/bin/expt004_old_format_record_list.rs:38] linkables: [ObsidianLinkableItem { item_data: Heading(H3, "2.1.1 Broken file-internal link"), event: Start(Heading { level: H3, id: None, classes: [], attrs: [] }) }, ObsidianLinkableItem { item_data: Heading(H3, "2.1.2 Broken Embedded text links"), event: Start(Heading { level: H3, id: None, classes: [], attrs: [] }) }, ObsidianLinkableItem { item_data: Heading(H3, "2.1.3 Comparison with how I made valid README internal file links"), event: Start(Heading { level: H3, id: None, classes: [], attrs: [] }) }, ObsidianLinkableItem { item_data: Heading(H3, "2.1.4 Case 1: Basic mapping and filtering"), event: Start(Heading { level: H3, id: None, classes: [], attrs: [] }) }, ObsidianLinkableItem { item_data: Heading(H3, "2.1.5 Beep"), event: Start(Heading { level: H3, id: None, classes: [], attrs: [] }) }, ObsidianLinkableItem { item_data: Heading(H3, "2.1.6 Beep"), event: Start(Heading { level: H3, id: None, classes: [], attrs: [] }) }, ObsidianLinkableItem { item_data: Heading(H3, "2.1.7 Test Some description and things"), event: Start(Heading { level: H3, id: None, classes: [], attrs: [] }) }]
```

Looks good!


 </file ./lan/tasks/2025/000 Note Repo Migration Sept 8/investigations/007 Debugging no old format records found in expt004.md>



 <file ./lan/tasks/2025/000 Note Repo Migration Sept 8/investigations/002 What's a directory entry from folder.read_dir in rust?.md>

---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[001 Parse through all notes and classify as core - peripheral - partial]]"
context_type: investigation
status: done
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[001 Parse through all notes and classify as core - peripheral - partial]] 

Spawned in: [[001 Parse through all notes and classify as core - peripheral - partial#^spawn-invst-c00179|^spawn-invst-c00179]]

# 1 Journal

2025-09-13 Wk 37 Sat - 16:23 +03:00

I'm trying

```rust
let dir = folder.read_dir().map_err(|e| e.to_string())?;
```

for some `PathBuf` folder.

And apparently you can get its entries?

```rust
let dir_entries = dir
	.collect::<Result<Vec<_>, _>>()
	.map_err(|e| e.to_string())?;
```

Let's create an example binary `ex002` for this.

Checked `/home/lan/src/cloned/gh/deltachives/2025-001-tut-diesel-rs/src/bin/get_post.rs` for basic argparsing in rust

2025-09-13 Wk 37 Sat - 16:35 +03:00

```rust
use std::env::args;
use std::path::PathBuf;

fn main() {
    let folder = args()
        .nth(1)
        .expect("Please provide a folder path")
        .parse::<PathBuf>()
        .expect("Failed to parse folder as a path");

    let dir = folder.read_dir().unwrap();

    let dir_entries = dir.collect::<Result<Vec<_>, _>>().unwrap();

    println!("{dir_entries:?}");
}
```

Let's try to run it

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin ex002_dir_entries "/home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration"

# out (relevant)
[DirEntry("/home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration/.gitignore"), DirEntry("/home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration/target"), DirEntry("/home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration/README.md"), DirEntry("/home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration/.git"), DirEntry("/home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration/src"), DirEntry("/home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration/Cargo.toml"), DirEntry("/home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration/Cargo.lock")]
```

So directories are also entries.

2025-09-13 Wk 37 Sat - 16:40 +03:00

```rust
use std::env::args;
use std::path::PathBuf;

fn main() {
    let folder = args()
        .nth(1)
        .expect("Please provide a folder path")
        .parse::<PathBuf>()
        .expect("Failed to parse folder as a path");

    let dir = folder.read_dir().unwrap();

    let dir_entries = dir.collect::<Result<Vec<_>, _>>().unwrap();

    let folder_entries = dir_entries
        .iter()
        .filter(|entry| entry.metadata().unwrap().is_dir())
        .collect::<Vec<_>>();

    let file_entries = dir_entries
        .iter()
        .filter(|entry| entry.metadata().unwrap().is_file())
        .collect::<Vec<_>>();

    println!("{folder_entries:?}\n");
    println!("{file_entries:?}");
}
```

Entry metadata shows it can be a file, directory, or symlink.

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin ex002_dir_entries "/home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration"

# out (relevant)
[DirEntry("/home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration/target"), DirEntry("/home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration/.git"), DirEntry("/home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration/src")]

[DirEntry("/home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration/.gitignore"), DirEntry("/home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration/README.md"), DirEntry("/home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration/Cargo.toml"), DirEntry("/home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration/Cargo.lock")]
```

 </file ./lan/tasks/2025/000 Note Repo Migration Sept 8/investigations/002 What's a directory entry from folder.read_dir in rust?.md>



 <file ./lan/tasks/2025/000 Note Repo Migration Sept 8/investigations/009 Investigate pulldown_cmark_to_cmark incorrectly converting item lists and bullet style.md>

---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[004 Writing events back to file with pulldown cmark]]"
context_type: investigation
status: done
---
#cmark #testing

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[004 Writing events back to file with pulldown cmark]] 

Spawned in: [[004 Writing events back to file with pulldown cmark#^spawn-invst-29ce0d|^spawn-invst-29ce0d]]

# 1 Journal

2025-09-23 Wk 39 Tue - 03:29 +03:00

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt006_markdown_to_markdown ~/src/cloned/gh/deltatraced/delta-trace/README.md > tmp.md
diff -u ~/src/cloned/gh/deltatraced/delta-trace/README.md tmp.md
rm tmp.md
```

```diff
 1. [1 What is this?](#1-what-is-this)
-2. [2 See also](#2-see-also)
-3. [3 Contributing](#3-contributing)
-4. [4 License](#4-license)
+1. [2 See also](#2-see-also)
+1. [3 Contributing](#3-contributing)
+1. [4 License](#4-license)
```

```diff
-2. [lan-setup-notes](https://github.com/LanHikari22/lan-setup-notes/tree/webview)
-       - A knowledge base for tooling, diagnosing, investigating, and setting up systems!
+1. [lan-setup-notes](https://github.com/LanHikari22/lan-setup-notes/tree/webview)
+   * A knowledge base for tooling, diagnosing, investigating, and setting up systems!
```


2025-09-23 Wk 39 Tue - 03:32 +03:00

Let's look at the events in question

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt000_parse_single_pulldown_cmark <(cat << 'EOF'
 1. [1 What is this?](#1-what-is-this)
 2. [2 See also](#2-see-also)
 3. [3 Contributing](#3-contributing)
 4. [4 License](#4-license)
EOF
)

# out
Event Start(List(Some(1)))
Event Start(Item)
Event Start(Link { link_type: Inline, dest_url: Borrowed("#1-what-is-this"), title: Borrowed(""), id: Borrowed("") })
Event Text(Borrowed("1 What is this?"))
Event End(Link)
Event End(Item)
Event Start(Item)
Event Start(Link { link_type: Inline, dest_url: Borrowed("#2-see-also"), title: Borrowed(""), id: Borrowed("") })
Event Text(Borrowed("2 See also"))
Event End(Link)
Event End(Item)
Event Start(Item)
Event Start(Link { link_type: Inline, dest_url: Borrowed("#3-contributing"), title: Borrowed(""), id: Borrowed("") })
Event Text(Borrowed("3 Contributing"))
Event End(Link)
Event End(Item)
Event Start(Item)
Event Start(Link { link_type: Inline, dest_url: Borrowed("#4-license"), title: Borrowed(""), id: Borrowed("") })
Event Text(Borrowed("4 License"))
Event End(Link)
Event End(Item)
Event End(List(true))
```

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt006_markdown_to_markdown <(cat << 'EOF'
 1. [1 What is this?](#1-what-is-this)
 2. [2 See also](#2-see-also)
 3. [3 Contributing](#3-contributing)
 4. [4 License](#4-license)
EOF
)

# out (modified)
B1. [1 What is this?](#1-what-is-this)
B1. [2 See also](#2-see-also)
B1. [3 Contributing](#3-contributing)
B1. [4 License](#4-license)
```

I had to add a `B` preceding the number here because obsidian will try to help you and number the items itself! Even inside a code block.

2025-09-23 Wk 39 Tue - 03:54 +03:00

This issue persists even when we get the events from the parser directly rather than `TextMergeStream`:

```diff
-let iter = TextMergeStream::new(parser);

-let events = iter.collect::<Vec<_>>();
+let events = parser.collect::<Vec<_>>();
```

Looking at the [default options](https://github.com/Byron/pulldown-cmark-to-cmark/blob/bf34a3cac68e6f82a24ee3d44224a9e2ef2bcd0d/src/lib.rs#L180), we find

```rust
Options {
    newlines_after_headline: 2,
    newlines_after_paragraph: 2,
    newlines_after_codeblock: 2,
    newlines_after_htmlblock: 1,
    newlines_after_table: 2,
    newlines_after_rule: 2,
    newlines_after_list: 2,
    newlines_after_blockquote: 2,
    newlines_after_rest: 1,
    newlines_after_metadata: 1,
    code_block_token_count: 4,
    code_block_token: '`',
    list_token: '*',
    ordered_list_token: '.',
    increment_ordered_list_bullets: false,
    emphasis_token: '*',
    strong_token: "**",
};
```

Specifically,

```rust
increment_ordered_list_bullets: false,
list_token: '*',
```

In our case we use `-` and we do increment bullets. So let's do that.

```rust
increment_ordered_list_bullets: true,
list_token: '-',
```

```rust
    let _ = cmark_with_options(
        events.iter(),
        &mut mut_out,
        pulldown_cmark_to_cmark::Options {
            newlines_after_headline: 2,
            newlines_after_paragraph: 2,
            newlines_after_codeblock: 2,
            newlines_after_htmlblock: 1,
            newlines_after_table: 2,
            newlines_after_rule: 2,
            newlines_after_list: 2,
            newlines_after_blockquote: 2,
            newlines_after_rest: 1,
            newlines_after_metadata: 1,
            code_block_token_count: 4,
            code_block_token: '`',
            list_token: '-',
            ordered_list_token: '.',
            increment_ordered_list_bullets: true,
            emphasis_token: '*',
            strong_token: "**",
        },
    )?;
```

Let's try again.

2025-09-23 Wk 39 Tue - 04:02 +03:00

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt006_markdown_to_markdown <(cat << 'EOF'
 1. [1 What is this?](#1-what-is-this)
 2. [2 See also](#2-see-also)
 3. [3 Contributing](#3-contributing)
 4. [4 License](#4-license)
EOF
)

# out
1. [1 What is this?](#1-what-is-this)
2. [2 See also](#2-see-also)
3. [3 Contributing](#3-contributing)
4. [4 License](#4-license)
```

Awesome!

Now the other issue.

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt006_markdown_to_markdown <(cat << 'EOF'
 1. [delta-trace](https://github.com/deltatraced/delta-trace/tree/webview) **You are here!**
	- A knowledge base for researching, developing, studying, and experimenting!
2. [lan-setup-notes](https://github.com/LanHikari22/lan-setup-notes/tree/webview)
	- A knowledge base for tooling, diagnosing, investigating, and setting up systems!
3. [dism-exe-notes](https://github.com/dism-exe/dism-exe-notes/tree/webview/lan)
	- A knowledge base for reverse engineering and analyzing Mega Man Battle Network games!
4. [goldensun-notes](https://github.com/FutureFractal/goldensun-notes/tree/webview/lan)
	- A knowledge base for reverse engineering and analyzing Golden Sun GBA games!
5. [dbmint-notes](https://github.com/dbmint/dbmint-notes/tree/webview)
	- A knowledge base for developing intuitive and type correct database tools in Rust!

EOF
)

# out
1. [delta-trace](https://github.com/deltatraced/delta-trace/tree/webview) **You are here!**
   - A knowledge base for researching, developing, studying, and experimenting!
2. [lan-setup-notes](https://github.com/LanHikari22/lan-setup-notes/tree/webview)
   - A knowledge base for tooling, diagnosing, investigating, and setting up systems!
3. [dism-exe-notes](https://github.com/dism-exe/dism-exe-notes/tree/webview/lan)
   - A knowledge base for reverse engineering and analyzing Mega Man Battle Network games!
4. [goldensun-notes](https://github.com/FutureFractal/goldensun-notes/tree/webview/lan)
   - A knowledge base for reverse engineering and analyzing Golden Sun GBA games!
5. [dbmint-notes](https://github.com/dbmint/dbmint-notes/tree/webview)
   - A knowledge base for developing intuitive and type correct database tools in Rust!
```

They're dashes as per our conventions!

 </file ./lan/tasks/2025/000 Note Repo Migration Sept 8/investigations/009 Investigate pulldown_cmark_to_cmark incorrectly converting item lists and bullet style.md>



 <file ./lan/tasks/2025/000 Note Repo Migration Sept 8/investigations/006 Debugging through expt004 not terminating.md>

---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[001 Parse through all notes and classify as core - peripheral - partial]]"
context_type: investigation
status: done
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[001 Parse through all notes and classify as core - peripheral - partial]] 

Spawned in: [[001 Parse through all notes and classify as core - peripheral - partial#^spawn-invst-59092e|^spawn-invst-59092e]]

# 1 Journal

2025-09-16 Wk 38 Tue - 15:10 +03:00

```rust
pub fn process_markdown_files_in_vault(vault_folder: &ObsidianVaultPath, process_markdown_file: impl Fn(&PathBuf) -> Option<()>) {
    let working_items =
        cluster_note::get_working_item_paths_in_vault(vault_folder).expect("Failed to get working items");

    log::trace!("0");
    for item in working_items {
        log::trace!("1 {item:?}");
        match item {
            cluster_note::WorkingPath::Note(normal_note_file_path) => {
                process_markdown_file(&normal_note_file_path.path);
            },
            cluster_note::WorkingPath::ClusterFolder {
                cluster_root_folder: _cluster_root_folder,
                core_note_file: _core_note_file,
                category_folders_with_peripheral_files: _category_folders_with_peripheral_files,
            } => {
                process_markdown_file(&_core_note_file.path);
                _category_folders_with_peripheral_files
                    .into_iter()
                    .flat_map(|(_, peripheral_files)| peripheral_files)
                    .for_each(|peripheral_file| {
                        process_markdown_file(&peripheral_file.path);
                    })
            },
        }
        log::trace!("/1");
    }
    log::trace!("2");
}
```

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt004_old_format_record_list ~/src/cloned/gh/LanHikari22/lan-setup-notes

# out (relevant)
```

It gets stuck at `1` in cluster folder with core note `/home/lan/src/cloned/gh/LanHikari22/lan-setup-notes/lan/tasks/2025/000 Note Repo Migration Sept 8`.

Which is this!

2025-09-16 Wk 38 Tue - 15:23 +03:00

```rust
            cluster_note::WorkingPath::ClusterFolder {
                cluster_root_folder: _cluster_root_folder,
                core_note_file: _core_note_file,
                category_folders_with_peripheral_files: _category_folders_with_peripheral_files,
            } => {
                log::trace!("3");
                process_markdown_file(&_core_note_file.path);
                log::trace!("3.1");
                _category_folders_with_peripheral_files
                    .into_iter()
                    .flat_map(|(_, peripheral_files)| peripheral_files)
                    .for_each(|peripheral_file| {
                        log::trace!("4");
                        process_markdown_file(&peripheral_file.path);
                        log::trace!("/4");
                    });
                log::trace!("/3");
            },
        }
```

We just hit `3`. 

In `expt004`, 

```rust
fn main() {
    let vault_folder = drivers::init_logging_and_get_obsidian_vault();
    let opt_note_path = drivers::get_opt_arg_note_path();

    let process_markdown_file = |path: &PathBuf, only_summarize: bool| -> Option<()> {
        log::trace!("A");
        let content = common::read_file_content(path)
            .expect("Could not read content");
        log::trace!("AA");

        let events = common::parse_markdown_file(&content);
        log::trace!("AAA");

        let old_format_records = cluster_note::get_note_old_format_entries(&events)?;
        log::trace!("B");

        let records_with_extracts = old_format_records
            .iter()
            .map(|entry| (
                entry,
                common::extract_linkable_obsidian_md_items(&entry.events),
                common::extract_obsidian_md_links(&entry.events),
            ))
            .collect::<Vec<_>>();

        if records_with_extracts.len() != 0 {
            log::trace!("path {path:?} has {} records", records_with_extracts.len());
        }

        log::trace!("C");

        for (record, linkables, links) in records_with_extracts {
            log::trace!("D");
            let spawn_metadata = cluster_note::extract_spawn_metadata_from_old_format(&linkables, &links);
            log::trace!("D1");

            log::trace!("record {:?} of {:?} has {} events.", record.entry_name, record.entry_type, record.events.len());
            log::trace!("\tIt has {} linkables, {} links, and {} spawn items.", linkables.len(), links.len(), spawn_metadata.len());

            if !only_summarize {
                log::trace!("record: {record:?}");
                log::trace!("linkables: {linkables:?}");
                log::trace!("links: {links:?}");
                log::trace!("spawn_metadata: {spawn_metadata:?}");
            }
            log::trace!("/D");
        }
        log::trace!("/C");

        Some(())
    };

    log::trace!("E");
    match opt_note_path {
        Some(note_path) => {
            process_markdown_file(&note_path, true);
        },
        None => {
            drivers::process_markdown_files_in_vault(&vault_folder, |path| process_markdown_file(path, false));
        },
    }
    log::trace!("/E");
}
```

We hit `3AAA`. 

So we're frozen at

```rust
let old_format_records = cluster_note::get_note_old_format_entries(&events)?;
```

2025-09-16 Wk 38 Tue - 15:36 +03:00

It was because we used not to advance the cursor when no sub H2 events are found like in this fix:

```rust
if mut_inner_cur - mut_cur > 0 {
	mut_grouped_events
		.push(Grouped::SubH2(&events[mut_cur..mut_inner_cur]));
	mut_cur = mut_inner_cur;
} else {
	mut_cur += 1;
}
```

So there might be cases where we don't register any SubH2s?

2025-09-16 Wk 38 Tue - 20:43 +03:00

```rust
if mut_inner_cur - mut_cur > 0 {
	mut_grouped_events
		.push(Grouped::SubH2(&events[mut_cur..mut_inner_cur]));
	mut_cur = mut_inner_cur;
} else {
	log::trace!("{:?}", events[mut_cur]);
	mut_cur += 0;
}
```

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt004_old_format_record_list ~/src/cloned/gh/LanHikari22/lan-setup-notes

# out
[2025-09-16 20:42:56 TRACE src/cluster_note.rs:573] Start(Heading { level: H2, id: None, classes: [], attrs: [] })
[infinite ...]
```

So we encounter an `H2`, at the current cursor, so why are we not handling that instead of sub `H2` items?

```rust
while mut_cur < events.len() {
	log::trace!("0A mut_cur {mut_cur} -> {:?}", events[mut_cur]);
	log::trace!("0B {:?}", comm::process_heading_event_of_level(&HeadingLevel::H1, &events[mut_cur..]));
	log::trace!("0C {:?}", comm::process_heading_event_of_level(&HeadingLevel::H2, &events[mut_cur..]));
	match comm::process_heading_event_of_level(&HeadingLevel::H1, &events[mut_cur..]) {
	
// [...]
if mut_inner_cur - mut_cur > 0 {
	mut_grouped_events
		.push(Grouped::SubH2(&events[mut_cur..mut_inner_cur]));
	mut_cur = mut_inner_cur;
} else {
	log::trace!("2 {:?}", events[mut_cur]);
	mut_cur += 0;
}
```

```
[2025-09-16 20:58:05 TRACE src/cluster_note.rs:523] 0A mut_cur 1 -> Start(Heading { level: H2, id: None, classes: [], attrs: [] })
[2025-09-16 20:58:05 TRACE src/cluster_note.rs:524] 0B None
[2025-09-16 20:58:05 TRACE src/cluster_note.rs:525] 0C None
[2025-09-16 20:58:05 TRACE src/cluster_note.rs:577] 1 Start(Heading { level: H2, id: None, classes: [], attrs: [] })
[infinite ...]
```

Even though it's an `H2`, `process_heading_event_of_level(H2)` won't recognize it!

2025-09-16 Wk 38 Tue - 21:03 +03:00

Let's make the errors for this more explicit rather than just None.

2025-09-16 Wk 38 Tue - 21:17 +03:00

```
[2025-09-16 21:26:24 TRACE src/cluster_note.rs:523] 0A mut_cur 1 -> Start(Heading { level: H2, id: None, classes: [], attrs: [] })
[2025-09-16 21:26:24 TRACE src/cluster_note.rs:524] 0B Err(WrongLevel(H1, H2))
[2025-09-16 21:26:24 TRACE src/cluster_note.rs:525] 0C Err(InvalidScheme(2, "SoftBreak"))
[2025-09-16 21:26:24 TRACE src/cluster_note.rs:577] 1 Start(Heading { level: H2, id: None, classes: [], attrs: [] })

[infinite ...]
```

Invalid scheme... Is our assumption of `H<N> Text /H<N>` invalid?

2025-09-16 Wk 38 Tue - 21:41 +03:00

We're creating `expt005` to reproduce some events from input markdown with [gh pulldown-cmark/pulldown-cmark](https://github.com/pulldown-cmark/pulldown-cmark/).

2025-09-16 Wk 38 Tue - 21:55 +03:00

It seems this is happening in `"/home/lan/src/cloned/gh/LanHikari22/lan-setup-notes/lan/tasks/2025/000 Note Repo Migration Sept 8/000 Note Repo Migration Sept 8.md"`

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt000_parse_single_pulldown_cmark ~/src/cloned/gh/LanHikari22/lan-setup-notes/lan/tasks/2025/000\ Note\ Repo\ Migration\ Sept\ 8/000\ Note\ Repo\ Migration\ Sept\ 8.md | less
```

It's in the very beginning.

```
Event Rule
Event Start(Heading { level: H2, id: None, classes: [], attrs: [] })
Event Text(Boxed("deprecates: \"[[001 Looking into heading level graph views]]\""))
Event SoftBreak
Event Text(Boxed("breaks: \"[[000 Setting up time logging in Obsidian]]\""))
Event End(Heading(H2))
```

That's the frontmatter. It's acting as if it's an H2.

It's reproduced with `expt005`.

```sh
cargo run --bin expt005_repro_heading_markdown_events
```

```rust
/*
	Event Rule
	Event Start(Heading { level: H2, id: None, classes: [], attrs: [] })
	Event Text(Boxed("deprecates: \"[[001 Looking into heading level graph views]]\""))
	Event SoftBreak
	Event Text(Boxed("breaks: \"[[000 Setting up time logging in Obsidian]]\""))
	Event End(Heading(H2))
 */
print_events(
	&r#"
	@ ---
	@ deprecates: "[[001 Looking into heading level graph views]]"
	@ breaks: "[[000 Setting up time logging in Obsidian]]"
	@ ---
	@ "#
	.replace("@ ","")
	.replace("        ","")
	,
);
```

2025-09-16 Wk 38 Tue - 22:08 +03:00

This should mean that this is working, it's just that we should be skipping this, as we have been. It's not an H2, and it's also not a subH2 after all.

2025-09-16 Wk 38 Tue - 22:16 +03:00

Okay so we are not always in a `SubH2` just because we fail to parse H1 or H2.

Since a frontmatter will only appear in the beginning of the file, we can just increment in case we haven't found any groups yet, since it's impossible to find a `SubH2` before an `H2` anyway.

2025-09-16 Wk 38 Tue - 22:20 +03:00

```rust
if mut_inner_cur - mut_cur > 0 {
	mut_grouped_events
		.push(Grouped::SubH2(&events[mut_cur..mut_inner_cur]));
	mut_cur = mut_inner_cur;
} else {
	// We should not get here. This should've been handled by H1 or H2.
	log::warn!("Could not get any content within H2: {:?}", &events[mut_cur..mut_cur+min(5, events.len())]);
	mut_cur += 1;
}
```

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt004_old_format_record_list ~/src/cloned/gh/LanHikari22/lan-setup-notes

# out (relevant)
[2025-09-16 22:26:08 WARN src/cluster_note.rs:575] Could not get any content within H2: [Start(Heading { level: H2, id: None, classes: [], attrs: [] }), Text(Borrowed("6.4 Issues with ")), Start(Link { link_type: Inline, dest_url: Borrowed("https://github.com/LanHikari22/lan-exp-scripts/blob/main/scripts/2025/Wk27-000-expedition-33-ui-auto/test.py"), title: Borrowed(""), id: Borrowed("") }), Text(Borrowed("ydotool cv test script")), End(Link)]
[2025-09-16 22:26:08 WARN src/cluster_note.rs:575] Could not get any content within H2: [Start(Heading { level: H2, id: None, classes: [], attrs: [] }), Text(Borrowed("7.1 On ")), Start(Link { link_type: Inline, dest_url: Borrowed("https://www.gtk.org/"), title: Borrowed(""), id: Borrowed("") }), Text(Borrowed("GTK")), End(Link)]
[2025-09-16 22:26:08 WARN src/cluster_note.rs:575] Could not get any content within H2: [Start(Heading { level: H2, id: None, classes: [], attrs: [] }), Text(Borrowed("4.1 (~1) Create ")), Code(Borrowed("open_timeline_log.js")), End(Heading(H2)), Start(Paragraph)]
```

It happens three times.

Headings with links in them! And Code!

2025-09-16 Wk 38 Tue - 22:31 +03:00

In `expt005`, 

```rust
    /*
        Event Start(Heading { level: H2, id: None, classes: [], attrs: [] })
        Event Text(Borrowed("This heading has "))
        Event Start(Link { link_type: Inline, dest_url: Borrowed("https://www.google.com"), title: Borrowed(""), id: Borrowed("") })
        Event Text(Borrowed("links"))
        Event End(Link)
        Event Text(Borrowed(" and "))
        Event Code(Borrowed("code"))
        Event Text(Borrowed(" in it! "))
        Event Code(Borrowed("Many"))
        Event Text(Borrowed(" "))
        Event Code(Borrowed("of"))
        Event Text(Borrowed(" "))
        Event Code(Borrowed("them"))
        Event Text(Borrowed("!"))
        Event End(Heading(H2))
     */
    print_events(
        &r#"
        @ ## This heading has [links](https://www.google.com) and `code` in it! `Many` `of` `them`!
        @ "#
        .replace("@ ","")
        .replace("        ","")
        ,
    );
```

This really breaks our assumption here.

2025-09-16 Wk 38 Tue - 22:41 +03:00

But those will not work for our migration. Let's remove these use cases!

2025-09-16 Wk 38 Tue - 22:47 +03:00

Okay, so we resolved the termination issue, and removed the weird headings that won't migrate well. There is still the fact that we're not finding any old format headings:

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt004_old_format_record_list ~/src/cloned/gh/LanHikari22/lan-setup-notes

# out (relevent)
[nothing]
```



 </file ./lan/tasks/2025/000 Note Repo Migration Sept 8/investigations/006 Debugging through expt004 not terminating.md>



 <file ./lan/tasks/2025/000 Note Repo Migration Sept 8/investigations/003 Investigating frontmatter to markdown event mappings with pulldown_cmark.md>

---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[001 Parse through all notes and classify as core - peripheral - partial]]"
context_type: investigation
status: done
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[001 Parse through all notes and classify as core - peripheral - partial]] 

Spawned in: [[001 Parse through all notes and classify as core - peripheral - partial#^spawn-invst-3d5e66|^spawn-invst-3d5e66]]

# 1 Journal

2025-09-13 Wk 37 Sat - 19:44 +03:00

Looking at expected markdown events for frontmatter by running `expt000`,

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt000_parse_single_pulldown_cmark ~/src/cloned/gh/LanHikari22/lan-setup-notes/lan/tasks/2025/000\ Note\ Repo\ Migration\ Sept\ 8/entries/000\ Break\ down\ lan-exp-scripts\ into\ an\ org.md | less
```

The frontmatter:

```
---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[001 Parse through all notes and classify as core - peripheral - partial]]"
context_type: entry
---
```

The events:

```
Event Start(Heading { level: H2, id: None, classes: [], attrs: [] })
Event Text(Boxed("parent: \"[[000 Note Repo Migration Sept 8]]\""))
Event SoftBreak
Event Text(Boxed("spawned_by: \"[[001 Parse through all notes and classify as core - peripheral - partial]]\""))
Event SoftBreak
Event Text(Borrowed("context_type: entry"))
Event End(Heading(H2))
```

There's also a single event at the beginning [`Event Rule`](https://docs.rs/pulldown-cmark/latest/pulldown_cmark/enum.Event.html#variant.Rule). This matches the `---`, though it does not include a second rule even though we end with `---`.

This seems consistent across different files.

So we expect `Rule H2 (Text [Softbreak] ...) /H2` to parse the frontmatter section, and it needs to be at the beginning of the file.


 </file ./lan/tasks/2025/000 Note Repo Migration Sept 8/investigations/003 Investigating frontmatter to markdown event mappings with pulldown_cmark.md>



 <file ./lan/tasks/2025/000 Note Repo Migration Sept 8/investigations/000 How should we parse markdown files in Rust?.md>

---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[001 Parse through all notes and classify as core - peripheral - partial]]"
context_type: investigation
status: done
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[001 Parse through all notes and classify as core - peripheral - partial]]

Spawned in: [[001 Parse through all notes and classify as core - peripheral - partial#^spawn-invst-38f502|^spawn-invst-38f502]]

# 1 Objective

We need to determine the method we will use to parse markdown files in our rust project.

# 2 Journal

2025-09-09 Wk 37 Tue - 14:53 +03:00

We could do something similar to [obsidian-export](https://github.com/zoni/obsidian-export). 

Spawn [[001 How does obsidian-export parse markdown in postprocessors?]] ^spawn-invst-4917e8

2025-09-09 Wk 37 Tue - 15:22 +03:00

Let's try pulldown cmark over obsidian notes and see what we get.

2025-09-09 Wk 37 Tue - 16:06 +03:00

This looks good. Especially since right now what we're interested in is just parsing headings. We will though have to worry about obsidian links.

 </file ./lan/tasks/2025/000 Note Repo Migration Sept 8/investigations/000 How should we parse markdown files in Rust?.md>



 <file ./lan/tasks/2025/000 Note Repo Migration Sept 8/investigations/001 How does obsidian-export parse markdown in postprocessors?.md>

---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[000 How should we parse markdown files in Rust?]]"
context_type: investigation
status: done
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[000 How should we parse markdown files in Rust?]]

Spawned in: [[000 How should we parse markdown files in Rust?#^spawn-invst-4917e8|^spawn-invst-4917e8]]

# 1 Journal

2025-09-09 Wk 37 Tue - 15:01 +03:00

The project is [gh zoni/obsidian-export](https://github.com/zoni/obsidian-export)

In their [Cargo.toml](https://github.com/zoni/obsidian-export/blob/main/Cargo.toml) we find that they use [gh pulldown-cmark/pulldown-cmark](https://github.com/pulldown-cmark/pulldown-cmark/).

2025-09-09 Wk 37 Tue - 15:13 +03:00

They have an explanation there about pull parsing. There's a [paper](https://www.xmlpull.org/history/index.html) (referenced from [xmlpull.org](https://www.xmlpull.org/)) explaining this.

They explain:

> With pull API the application is in control and requires next XML event from the parser when it is ready to process it. That means that structure of code that is doing parsing _reflects_ structure of XML documents. This is fundamental pattern visible in all applications using pull based APIs and leads to easier to understand code.

2025-09-09 Wk 37 Tue - 15:20 +03:00

Spawn [[002 Parse a single obsidian markdown file with pulldown cmark]] ^spawn-task-0d2c20


 </file ./lan/tasks/2025/000 Note Repo Migration Sept 8/investigations/001 How does obsidian-export parse markdown in postprocessors?.md>



 <file ./lan/tasks/2025/000 Note Repo Migration Sept 8/investigations/004 Debugging through migration expt003 errors.md>

---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[001 Parse through all notes and classify as core - peripheral - partial]]"
context_type: investigation
status: done
---
#debugging

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[001 Parse through all notes and classify as core - peripheral - partial]] 

Spawned in: [[001 Parse through all notes and classify as core - peripheral - partial#^spawn-invst-0f35dc|^spawn-invst-0f35dc]]

# 1 Journal

2025-09-13 Wk 37 Sat - 23:40 +03:00

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt003_working_items_of_vault ~/src/cloned/gh/LanHikari22/lan-setup-notes/lan

# out (error, expected)
Folder passed is not a valid obsidian vault
```

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt003_working_items_of_vault ~/src/cloned/gh/LanHikari22/lan-setup-notes

# out (error, unexpected)
Failed to get working items
```

Okay, time to get some more detailed logging!

2025-09-14 Wk 37 Sun - 00:01 +03:00

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo add log
```

Checked [docs.rs log](https://docs.rs/log/latest/log/)

2025-09-14 Wk 37 Sun - 00:34 +03:00

Hmm, there are a lot of `?` operators for possible failures. We can find a failure with `:%s/?/.unwrap()/g`

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt003_working_items_of_vault ~/src/cloned/gh/LanHikari22/lan-setup-notes

# out (error, relevant)
thread 'main' panicked at src/cluster_note.rs:348:87:
called `Option::unwrap()` on a `None` value
```

We're failing at parsing a `NormalNoteFilePath` when we expect to just encounter a non-cluster related note.

We can revert the unwraps with `:%s/.unwrap\(\)/?/g`. This works for me because I didn't use unwraps in the file.

2025-09-14 Wk 37 Sun - 00:46 +03:00

We need to use

```sh
log::set_max_level(log::LevelFilter::Trace);
```

But we also need to configure this for stdout. We can also use [fstdout-logger](https://docs.rs/fstdout-logger/latest/fstdout_logger/) for defaults, file logs, and apparently even colored outputs.

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo add fstdout-logger
```

```rust
    let config = LoggerConfig::builder()
        .level(LevelFilter::Trace)
        .use_colors(true)
        .build();

    init_logger_with_config(Some("debug.log"), config).expect("Failed to initialize logger");

    log::info!("Hiiiii");

```

![[Pasted image 20250914004956.png]]

Cool!

2025-09-14 Wk 37 Sun - 00:52 +03:00

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt003_working_items_of_vault ~/src/cloned/gh/LanHikari22/lan-setup-notes

# out (error, relevant)
[00:52:00 DEBUG src/cluster_note.rs:157] path: "/home/lan/src/cloned/gh/LanHikari22/lan-setup-notes/.gitignore"
thread 'main' panicked at src/bin/expt003_working_items_of_vault.rs:27:10:
```

So we are failing on non-markdown files instead of just skipping them. Let's fix that.

2025-09-14 Wk 37 Sun - 00:58 +03:00

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt003_working_items_of_vault ~/src/cloned/gh/LanHikari22/lan-setup-notes

# out (error, relevant)
[00:58:03 ERROR src/cluster_note.rs:207] not a core file path!: "/home/lan/src/cloned/gh/LanHikari22/lan-setup-notes/lan/tasks/2025/000 Note Repo Migration Sept 8/000 Note Repo Migration Sept 8.md"
```

It should be.

```rust
pub fn is_cluster_core_file_path(path: &PathBuf) -> Option<bool> {
    if !path.is_file() || !path.exists() || !path.ends_with(".md") {
        log::error!("Not a markdown file: {path:?}");
        log::error!("is_file: {}", path.is_file());
        log::error!("exits: {}", path.exists());
        log::error!("ends_with_md: {}", path.ends_with(".md"));
        return Some(false);
    }
```

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt003_working_items_of_vault ~/src/cloned/gh/LanHikari22/lan-setup-notes

# out (error, relevant)
[01:02:20 ERROR src/cluster_note.rs:130] Not a markdown file: "/home/lan/src/cloned/gh/LanHikari22/lan-setup-notes/lan/tasks/2025/000 Note Repo Migration Sept 8/000 Note Repo Migration Sept 8.md"
[01:02:20 ERROR src/cluster_note.rs:131] is_file: true
[01:02:20 ERROR src/cluster_note.rs:132] exits: true
[01:02:20 ERROR src/cluster_note.rs:133] ends_with_md: false
```

I guess I missed this with the [`ends_with`](https://doc.rust-lang.org/std/path/struct.Path.html#examples) docs:

```
assert!(!path.ends_with("conf")); // use .extension() instead
```

2025-09-14 Wk 37 Sun - 01:17 +03:00

```rust
pub fn is_normal_markdown_file_path(path: &PathBuf) -> Option<bool> {
    log::trace!("path: {path:?}");
    if !path.is_file() || !path.exists() || path.extension().unwrap() != "md" {
        return Some(false);
    }
```

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt003_working_items_of_vault ~/src/cloned/gh/LanHikari22/lan-setup-notes

# out (error, relevant)
[01:16:53 TRACE src/cluster_note.rs:157] path: "/home/lan/src/cloned/gh/LanHikari22/lan-setup-notes/.gitignore"
```

`.gitignore` is still gonna fail the `.extension()` check.

We can't make this assumption:

```sh
if !path.is_file() || !path.exists() || path.extension()? != "md" {
	return Some(false);
}
```

If there's no extension, we just output false.

2025-09-14 Wk 37 Sun - 01:32 +03:00

```
pub fn is_cluster_category_folder(folder: &PathBuf) -> Option<bool> {
    log::trace!("0 folder: {folder:?}");
    let folder_name = folder.file_name().unwrap().to_str().unwrap();

    log::trace!("1 folder: {folder:?}");

    if !CONTEXT_TYPE_FOLDERS.contains(&folder_name) {
        return Some(false);
    }

    log::trace!("2 folder: {folder:?}");

    if !is_cluster_root_folder(&folder.parent().unwrap().to_path_buf()).unwrap() {
        return Some(false);
    }
    log::trace!("OK folder: {folder:?}");

    Some(true)
}
```

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt003_working_items_of_vault ~/src/cloned/gh/LanHikari22/lan-setup-notes

# out (error, relevant)
[01:32:01 TRACE src/cluster_note.rs:129] OK folder: "/home/lan/src/cloned/gh/LanHikari22/lan-setup-notes/lan/topics/tooling/windows/tasks/2025/000 Fix Windows Laptop not booting/inferences"
[01:32:01 TRACE src/cluster_note.rs:118] 1 folder: "/home/lan/src/cloned/gh/LanHikari22/lan-setup-notes/lan/topics/tooling/windows/tasks/2025/000 Fix Windows Laptop not booting/side-notes"
```

Okay, this is an outdated cluster note category we forgot to change. Let's change it in the vault.

2025-09-14 Wk 37 Sun - 01:40 +03:00

OK! Now we get all working items!

 </file ./lan/tasks/2025/000 Note Repo Migration Sept 8/investigations/004 Debugging through migration expt003 errors.md>



 <file ./lan/tasks/2025/000 Note Repo Migration Sept 8/investigations/010 Investigate pulldown-cmark-to-cmark adding 3 spaces for list items instead of 4.md>

---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[011 Create integration tests for obsidian patch fixes for sept 8 migration]]"
context_type: investigation
status: done
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[011 Create integration tests for obsidian patch fixes for sept 8 migration]] 

Spawned in: [[011 Create integration tests for obsidian patch fixes for sept 8 migration#^spawn-invst-52493f|^spawn-invst-52493f]]

# 1 Journal

2025-09-30 Wk 40 Tue - 03:47 +03:00

This is an [example](https://github.com/Byron/pulldown-cmark-to-cmark/blob/bf34a3cac68e6f82a24ee3d44224a9e2ef2bcd0d/src/lib.rs#L566C41-L566C45) from [gh Byron/pulldown-cmark-to-cmark](https://github.com/Byron/pulldown-cmark-to-cmark/tree/main) where they hardcode adding three spaces `"   "`. 

I found it being done for code blocks, and [footnote definition](https://github.com/Byron/pulldown-cmark-to-cmark/blob/bf34a3cac68e6f82a24ee3d44224a9e2ef2bcd0d/src/lib.rs#L513).

2025-09-30 Wk 40 Tue - 04:10 +03:00

We're trying to find why it's adding 3 spaces for nested list bullets.

```
[2025-09-30T01:16:47Z TRACE migration_rs::common] <old_content>
    1. Apples
        - Come in many colors
    2. Fruit
        - Come in many flavors
    </old_content>
[2025-09-30T01:16:47Z TRACE migration_rs::common] <new_content>
    3. Apples
       - Come in many colors
    2. Fruit
       - Come in many flavors
    </new_content>

<events>
Start(List(Some(1)))
Start(Item)
Text(Borrowed("Apples"))
Start(List(None))
Start(Item)
Text(Borrowed("Come in many colors"))
End(Item)
End(List(false))
End(Item)
Start(Item)
Text(Borrowed("Fruit"))
Start(List(None))
Start(Item)
Text(Borrowed("Come in many flavors"))
End(Item)
End(List(false))
End(Item)
End(List(true))
</events>
```

No sign for `"   "` added to texts in their [Text variant](https://github.com/Byron/pulldown-cmark-to-cmark/blob/bf34a3cac68e6f82a24ee3d44224a9e2ef2bcd0d/src/lib.rs#L835).

2025-09-30 Wk 40 Tue - 04:22 +03:00

From [Start(Item)](https://github.com/Byron/pulldown-cmark-to-cmark/blob/bf34a3cac68e6f82a24ee3d44224a9e2ef2bcd0d/src/lib.rs#L402) we can expect a space after the list token `*`/`-`, but still no mention of the spaces before it.

[fn list_item_padding_of](https://github.com/Byron/pulldown-cmark-to-cmark/blob/bf34a3cac68e6f82a24ee3d44224a9e2ef2bcd0d/src/text_modifications.rs#L74) Seems to `n` and pads that many spaces + 2? For some reason they have a period there with a space: `{n}. `. We know the period is because `n` really means the bullet number, like `3.`.  I guess it just needs to know how much spacing something like `2. ` and `125. ` take, but this is still not affecting the spaces *before* the item.

And they do nothing for the [start of a list](https://github.com/Byron/pulldown-cmark-to-cmark/blob/bf34a3cac68e6f82a24ee3d44224a9e2ef2bcd0d/src/lib.rs#L586). And nothing relevant for [end of item/list](https://github.com/Byron/pulldown-cmark-to-cmark/blob/bf34a3cac68e6f82a24ee3d44224a9e2ef2bcd0d/src/lib.rs#L797).

2025-09-30 Wk 40 Tue - 04:46 +03:00

They explain the meaning of [padding](https://github.com/Byron/pulldown-cmark-to-cmark/blob/bf34a3cac68e6f82a24ee3d44224a9e2ef2bcd0d/src/lib.rs#L60) in `pub struct State`, saying it's printed after each new line.

If we assume that [fn list_item_padding_of](https://github.com/Byron/pulldown-cmark-to-cmark/blob/bf34a3cac68e6f82a24ee3d44224a9e2ef2bcd0d/src/text_modifications.rs#L74) is called with `l` being `None` since this is not a numbered bullet, this would say that two spaces should be added. But if it were a numbered bullet, it might add 3 spaces given that a single digit takes a space, a dot takes a space, and with the extra space, we have 3: `1. ` $\to$ `   ` . It could have inherited 3-spaces from the *previous* numbered list. 

To confirm this, we need to see what happens when we cross the double digits in numbered bullets. Let's add a test case `list-002` 

2025-09-30 Wk 40 Tue - 05:01 +03:00

The test case `list-002` seems to confirm it.

```
[2025-09-30T02:00:08Z TRACE migration_rs::common] <old_content>
    1. Item
        - Desc
    2. Item
    3. Item
    4. Item
    5. Item
    6. Item
    7. Item
    8. Item
    9. Item
    10. Item
        - Desc
    11. Item
    12. Item
    </old_content>
```

```
[2025-09-30T02:00:08Z TRACE migration_rs::common] <new_content>
    1. Item
       - Desc
    2. Item
    3. Item
    4. Item
    5. Item
    6. Item
    7. Item
    8. Item
    9. Item
    10. Item
        - Desc
    11. Item
    12. Item
    </new_content>
```

Notice that `- Desc` gets only 3 space padding for 

2025-09-30 Wk 40 Tue - 05:11 +03:00

And if you change the number of spaces for Item 10 description from 4 to 3, it adds news lines before and after.

```
[2025-09-30T02:10:44Z TRACE migration_rs::common] <old_content>
    1. Item
        - Desc
    2. Item
    3. Item
    4. Item
    5. Item
    6. Item
    7. Item
    8. Item
    9. Item
    10. Item
       - Desc
    11. Item
    12. Item
    </old_content>
```

```
[2025-09-30T02:10:44Z TRACE migration_rs::common] <new_content>
    1. Item
       - Desc
    2. Item
    3. Item
    4. Item
    5. Item
    6. Item
    7. Item
    8. Item
    9. Item
    10. Item

    - Desc

    11. Item
    12. Item
    </new_content>
```


```
// Events on [4, 7] spaces for Item 10 Desc
// Events on 1 '\t' for Item 10 Desc
Start(Item)
Text(Borrowed("Item"))
Start(List(None))
Start(Item)
Text(Borrowed("Desc"))
End(Item)
End(List(false))
End(Item)

// Events on 3 spaces for Item 10 Desc
Start(Item)
Text(Borrowed("Item"))
End(Item)
End(List(true))
Start(List(None))
Start(Item)
Text(Borrowed("Desc"))
End(Item)
End(List(false))

// Events on 8 spaces for Item 10 Desc
Start(Item)
Text(Borrowed("Item"))
SoftBreak
Text(Borrowed("- Desc"))
End(Item)
```

So [gh pulldown-cmark/pulldown-cmark](https://github.com/pulldown-cmark/pulldown-cmark) is itself very particular about the number of spaces added there, even before any rendering.

2025-09-30 Wk 40 Tue - 05:46 +03:00

[spec CommonMark list-items](https://spec.commonmark.org/0.31.2/#list-items) specifies the rules and mentions about items having to at least include the width of the list marker $W$. 



 </file ./lan/tasks/2025/000 Note Repo Migration Sept 8/investigations/010 Investigate pulldown-cmark-to-cmark adding 3 spaces for list items instead of 4.md>



 <file ./lan/tasks/2025/000 Note Repo Migration Sept 8/investigations/005 Determining pulldown cmark events for old entries.md>

---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[001 Parse through all notes and classify as core - peripheral - partial]]"
context_type: investigation
status: done
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[001 Parse through all notes and classify as core - peripheral - partial]] 

Spawned in: [[001 Parse through all notes and classify as core - peripheral - partial#^spawn-invst-7d671c|^spawn-invst-7d671c]]

# 1 Journal

2025-09-14 Wk 37 Sun - 03:47 +03:00

2025-09-14 Wk 37 Sun - 10:37 +03:00

There's complexity to be considered when extracting portions of files into their own files. 

We need to track all linkable block identifiers in them and headings, and if those are referenced anywhere, they need to be updated against the new note spawned.

We need to preserve already existing spawn lineages and replace the "Spawn" lines with new spawned item links. Note that early on, I was inconsistent with how to do spawns, so don't expect a constant format. It should be consistent to find spawn block identifiers `^spawn-{cat}-{randhex6}` Call that `{spawn_block_identifier}`. 

If we find lines like `Spawn {note} {spawn_block_identifier}`, then they need to be updated with the new note link. 

2025-09-14 Wk 37 Sun - 13:04 +03:00

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt000_parse_single_pulldown_cmark ~/src/cloned/gh/LanHikari22/lan-setup-notes/lan/topics/tooling/mobile/entries/2025/000\ Install\ open\ source\ mobile\ OS\ on\ my\ phone.md  | less
```

This has 3 tasks, 1 side note. The tasks reference the others in multiple places.

There are also no Spawns. Notice that spawning itself was introduced later as a concept and practice.

We also have autonumbered headers, and the autonumbering needs to be dismissed.

```
Event Start(Heading { level: H1, id: None, classes: [], attrs: [] })
Event Text(Borrowed("3 Tasks"))
Event End(Heading(H1))
```

Once we find an H1 of interest marked like this, we can start grabbing all the events of H2s within it.

```
Event Start(Heading { level: H2, id: None, classes: [], attrs: [] })
Event Text(Borrowed("3.1 Install GrapheneOS on my Pixel 3"))
Event End(Heading(H2))
```

```
Event Start(Heading { level: H2, id: None, classes: [], attrs: [] })
Event Text(Borrowed("3.2 Install LineageOS on my Pixel 3"))
Event End(Heading(H2))
```

All events between those two are the content for the first task. The last item will go either to end of events or to another H2 or H1.

We should start by parsing all the content of those records, and all defined block identifiers and headings in their content. Those are the two ways anything in that segment may be linked.


 </file ./lan/tasks/2025/000 Note Repo Migration Sept 8/investigations/005 Determining pulldown cmark events for old entries.md>



 <file ./lan/tasks/2025/000 Note Repo Migration Sept 8/howtos/003 Rename github origin master branch to main.md>

---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[005 Create Spawn Note Command]]"
context_type: howto
status: done
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[005 Create Spawn Note Command]]

Spawned in: [[005 Create Spawn Note Command#^spawn-howto-ea5401|^spawn-howto-ea5401]]

# 1 Journal

2025-09-11 Wk 37 Thu - 03:29 +03:00

This is mostly an issue to be handled on the github side.

We need to rename the master branch to main in the project settings, and make sure that main is not pushed or delete it from origin with `git push origin --delete main`.

![[Pasted image 20250911032929.png]]

Then it gives a notification in the repo

![[Pasted image 20250911033153.png]]



 </file ./lan/tasks/2025/000 Note Repo Migration Sept 8/howtos/003 Rename github origin master branch to main.md>



 <file ./lan/tasks/2025/000 Note Repo Migration Sept 8/howtos/000 Include all crate features in build and vscode.md>

---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[003 Make everything under lan_rs_common featured for very minimal includes]]"
context_type: howto
status: done
---
Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[003 Make everything under lan_rs_common featured for very minimal includes]]

Spawned in: [[003 Make everything under lan_rs_common featured for very minimal includes#^spawn-howto-358a72|^spawn-howto-358a72]]

# 1 Journal

2025-09-09 Wk 37 Tue - 19:38 +03:00

I have a vscode `.vscode/settings.json` file

```json
{
  "rust-analyzer.cargo.features": [
  ]
}
```

But what do I put in there to ensure all features are included?

Here are the [cargo book docs on features](https://doc.rust-lang.org/cargo/reference/features.html).

Over CLI, we can use

> - `--all-features`: Activates all features of all packages selected on the command line.

Now what about vscode?

This issue [gh rust-lang/rust-analyzer #19630](https://github.com/rust-lang/rust-analyzer/issues/19630) mentions that it is possible to pass "all" to `"rust-analyzer.cargo.features"`

We could use `dep:crate_name` instead of all the `use_crate_name` features we made, though it doesn't seem like vscode recognizes it.

 </file ./lan/tasks/2025/000 Note Repo Migration Sept 8/howtos/000 Include all crate features in build and vscode.md>



 <file ./lan/tasks/2025/000 Note Repo Migration Sept 8/howtos/002 Copying folder structure but modifying some files in Rust.md>

---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[001 Parse through all notes and classify as core - peripheral - partial]]"
context_type: howto
status: done
resolved: partial
---
Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[001 Parse through all notes and classify as core - peripheral - partial]]

Spawned in: [[001 Parse through all notes and classify as core - peripheral - partial#^spawn-howto-9a69d0|^spawn-howto-9a69d0]]

# 1 Objective

Instead of globbing, we should consider taking a repository and outputting a repository for our migration. No in-place modifications. For many files, like `.obsidian/*` or `.git/*`, we only want to PASS so they end up simply being copied. For markdown files of interest, we may want to change them. A file may turn into a folder at current directory, and a folder at the current directory may turn into a file.

# 2 Journal

2025-09-10 Wk 37 Wed - 01:23 +03:00

This [blog post](https://nick.groenen.me/notes/recursively-copy-files-in-rust/) and [stackoverflow answer](https://stackoverflow.com/a/65192210/6944447) point to a similar example:

```rust
/// Copy files from source to destination recursively.
pub fn copy_recursively(source: impl AsRef<Path>, destination: impl AsRef<Path>) -> io::Result<()> {
    fs::create_dir_all(&destination)?;
    for entry in fs::read_dir(source)? {
        let entry = entry?;
        let filetype = entry.file_type()?;
        if filetype.is_dir() {
            copy_recursively(entry.path(), destination.as_ref().join(entry.file_name()))?;
        } else {
            fs::copy(entry.path(), destination.as_ref().join(entry.file_name()))?;
        }
    }
    Ok(())
}
```

But this doesn't resolve our issue alone. 

For our use case,

We might be able to use this to copy everything recursively and then modify selective files in the new directory.

We can then delete files, and replace them with folders, or scan for folders of an expected structure and replace them individually with system operations.

 </file ./lan/tasks/2025/000 Note Repo Migration Sept 8/howtos/002 Copying folder structure but modifying some files in Rust.md>



 <file ./lan/tasks/2025/000 Note Repo Migration Sept 8/howtos/001 Specifying arguments and returns in rust function docs.md>

---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[001 Parse through all notes and classify as core - peripheral - partial]]"
context_type: howto
status: done
---
Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[001 Parse through all notes and classify as core - peripheral - partial]]

Spawned in: [[001 Parse through all notes and classify as core - peripheral - partial#^spawn-howto-6ff1c8|^spawn-howto-6ff1c8]]

# 1 Journal

2025-09-09 Wk 37 Tue - 23:20 +03:00

There's some examples in ([stackoverflow post](https://stackoverflow.com/questions/30009650/how-to-document-rust-function-arguments) $\to$) [rust-by-example Documentation](https://doc.rust-lang.org/beta/rust-by-example/meta/doc.html)

It seems they do not rely on the style of documenting argument by argument here but instead just write what the function does and maybe also add examples.



 </file ./lan/tasks/2025/000 Note Repo Migration Sept 8/howtos/001 Specifying arguments and returns in rust function docs.md>



 <file ./lan/tasks/2025/000 Note Repo Migration Sept 8/howtos/004 Writing events back to file with pulldown cmark.md>

---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[009 Impl and carry out cluster note migration]]"
context_type: howto
status: done
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[009 Impl and carry out cluster note migration]] 

Spawned in: [[009 Impl and carry out cluster note migration#^spawn-howto-6ee267|^spawn-howto-6ee267]]

# 1 Journal

2025-09-22 Wk 39 Mon - 22:29 +03:00

[pulldown-cmark guide](https://pulldown-cmark.github.io/pulldown-cmark/). [gh pulldown-cmark/pulldown-cmark](https://github.com/pulldown-cmark/pulldown-cmark/).

2025-09-22 Wk 39 Mon - 22:34 +03:00

From their [event-filter.rs](https://github.com/pulldown-cmark/pulldown-cmark/blob/master/pulldown-cmark/examples/event-filter.rs) example, 

it seems that we should be writing whole parsers to files, and filter events out of parsers.  But it seems all the examples use `pulldown_cmark::html::push_html`, when we would like to push markdown again. 

We need to make an experiment `expt006` for this to guarantee we can read and write the same markdown file roundabout. markdown $\to$ events $\to$ markdown, and get an identical result.

2025-09-22 Wk 39 Mon - 23:01 +03:00

Spawn [[000 Can create markdown guides with mdbook]] ^spawn-idea-e6399b

2025-09-22 Wk 39 Mon - 23:04 +03:00

From the [developer guide](https://pulldown-cmark.github.io/pulldown-cmark/dev/index.html#developer-guide),

> pulldown-cmark uses a two-pass parsing strategy with a pull parser architecture to efficiently parse Markdown into HTML.

It doesn't seem they intend to write back to markdown.

Spawn [[008 How does obsidian-export export pulldown-cmark back to markdown?]] ^spawn-invst-ebcfc2

2025-09-22 Wk 39 Mon - 23:54 +03:00

So they use [pulldown_cmark_to_cmark](https://docs.rs/pulldown-cmark-to-cmark/latest/pulldown_cmark_to_cmark/) ([lib.rs](https://lib.rs/crates/pulldown-cmark-to-cmark) [gh](https://github.com/byron/pulldown-cmark-to-cmark)).

2025-09-23 Wk 39 Tue - 03:03 +03:00

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo add pulldown-cmark-to-cmark
```

2025-09-23 Wk 39 Tue - 03:14 +03:00

Created `render_events_to_common_markdown` which uses `pulldown_cmark_to_cmark`. 

But given the default options present

```rust
const DEFAULT_OPTIONS: Options<'_> = Options {
    newlines_after_headline: 2,
    newlines_after_paragraph: 2,
    newlines_after_codeblock: 2,
    newlines_after_htmlblock: 1,
    newlines_after_table: 2,
    newlines_after_rule: 2,
    newlines_after_list: 2,
    newlines_after_blockquote: 2,
    newlines_after_rest: 1,
    newlines_after_metadata: 1,
    code_block_token_count: 4,
    code_block_token: '`',
    list_token: '*',
    ordered_list_token: '.',
    increment_ordered_list_bullets: false,
    emphasis_token: '*',
    strong_token: "**",
};
```

I'm not sure if this will produce an identical file out.

2025-09-23 Wk 39 Tue - 03:17 +03:00

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt006_markdown_to_markdown ~/src/cloned/gh/deltatraced/delta-trace/README.md > tmp.md
sha1sum tmp.md ~/src/cloned/gh/deltatraced/delta-trace/README.md 

# out
df78eb36b109c278d5471b862118523163b652bf  tmp.md
3de07fb211832fcab9ccd1f659719fd917160d6a  /home/lan/src/cloned/gh/deltatraced/delta-trace/README.md
```

So they're different, but how?

```diff
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
diff -u ~/src/cloned/gh/deltatraced/delta-trace/README.md tmp.md
```

```diff
 </p>

-
 <div align="center">
   <sub>Built with ❤︎ by Mohammed Alzakariya</sub>
 </div>

-
 1. [1 What is this?](#1-what-is-this)
```

It removed double spaces.

```diff
 1. [1 What is this?](#1-what-is-this)
-2. [2 See also](#2-see-also)
-3. [3 Contributing](#3-contributing)
-4. [4 License](#4-license)
+1. [2 See also](#2-see-also)
+1. [3 Contributing](#3-contributing)
+1. [4 License](#4-license)
```

It messed up the numbering, but why?

```diff
-2. [lan-setup-notes](https://github.com/LanHikari22/lan-setup-notes/tree/webview)
-       - A knowledge base for tooling, diagnosing, investigating, and setting up systems!
+1. [lan-setup-notes](https://github.com/LanHikari22/lan-setup-notes/tree/webview)
+   * A knowledge base for tooling, diagnosing, investigating, and setting up systems!
```

Besides messing up the numbering, `-` are replaced with `*` .

```diff
-All contributions are welcome! 
+All contributions are welcome!
```

It trims lines. so the ` ` at the end there is removed.

2025-09-23 Wk 39 Tue - 03:27 +03:00

Spawn [[009 Investigate pulldown_cmark_to_cmark incorrectly converting item lists and bullet style]] ^spawn-invst-29ce0d

2025-09-23 Wk 39 Tue - 04:05 +03:00

So it was mostly just due to the default settings. Let's try again, the remaining issues like with trimmed lines and removal of empty lines are of no concern to us.

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt006_markdown_to_markdown ~/src/cloned/gh/deltatraced/delta-trace/README.md > tmp.md
diff -u ~/src/cloned/gh/deltatraced/delta-trace/README.md tmp.md
rm tmp.md
```

```diff
 1. [delta-trace](https://github.com/deltatraced/delta-trace/tree/webview) **You are here!**
-       - A knowledge base for researching, developing, studying, and experimenting!
+   - A knowledge base for researching, developing, studying, and experimenting!
 2. [lan-setup-notes](https://github.com/LanHikari22/lan-setup-notes/tree/webview)
-       - A knowledge base for tooling, diagnosing, investigating, and setting up systems!
+   - A knowledge base for tooling, diagnosing, investigating, and setting up systems!
```

The nesting level is different...

Don't to write to the place you read from at once, it would give you an empty file:

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration

# (bad)
cargo run --bin expt006_markdown_to_markdown ~/src/cloned/gh/deltatraced/delta-trace/README.md > ~/src/cloned/gh/deltatraced/delta-trace/README.md
```

Good thing we back up the repositories before making changes to them, so this is easy to revert with `git reset --hard` at delta-trace.

2025-09-23 Wk 39 Tue - 04:14 +03:00

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt006_markdown_to_markdown ~/src/cloned/gh/deltatraced/delta-trace/README.md > tmp.md && mv tmp.md ~/src/cloned/gh/deltatraced/delta-trace/README.md
```

Despite the different spacing, the obsidian presentation is identical.

This should sufficiently cover how to convert back from events to markdown files.


 </file ./lan/tasks/2025/000 Note Repo Migration Sept 8/howtos/004 Writing events back to file with pulldown cmark.md>

