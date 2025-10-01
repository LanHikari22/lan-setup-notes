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

ke
