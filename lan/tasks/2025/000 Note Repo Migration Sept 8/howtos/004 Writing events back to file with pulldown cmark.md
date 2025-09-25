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
