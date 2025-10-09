---
parent: '[[000 Note Repo Migration Sept 8]]'
spawned_by: '[[004 Writing events back to file with pulldown cmark]]'
context_type: investigation
status: done
---

\#cmark #testing

Parent: [000 Note Repo Migration Sept 8](../000%20Note%20Repo%20Migration%20Sept%208.md)

Spawned by: [004 Writing events back to file with pulldown cmark](../howtos/004%20Writing%20events%20back%20to%20file%20with%20pulldown%20cmark.md)

Spawned in: [<a name="spawn-invst-29ce0d" />^spawn-invst-29ce0d](../howtos/004%20Writing%20events%20back%20to%20file%20with%20pulldown%20cmark.md#spawn-invst-29ce0d)

# 1 Journal

2025-09-23 Wk 39 Tue - 03:29 +03:00

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt006_markdown_to_markdown ~/src/cloned/gh/deltatraced/delta-trace/README.md > tmp.md
diff -u ~/src/cloned/gh/deltatraced/delta-trace/README.md tmp.md
rm tmp.md
````

````diff
 1. [1 What is this?](#1-what-is-this)
-2. [2 See also](#2-see-also)
-3. [3 Contributing](#3-contributing)
-4. [4 License](#4-license)
+1. [2 See also](#2-see-also)
+1. [3 Contributing](#3-contributing)
+1. [4 License](#4-license)
````

````diff
-2. [lan-setup-notes](https://github.com/LanHikari22/lan-setup-notes/tree/webview)
-       - A knowledge base for tooling, diagnosing, investigating, and setting up systems!
+1. [lan-setup-notes](https://github.com/LanHikari22/lan-setup-notes/tree/webview)
+   * A knowledge base for tooling, diagnosing, investigating, and setting up systems!
````

2025-09-23 Wk 39 Tue - 03:32 +03:00

Let's look at the events in question

````sh
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
````

````sh
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
````

I had to add a `B` preceding the number here because obsidian will try to help you and number the items itself! Even inside a code block.

2025-09-23 Wk 39 Tue - 03:54 +03:00

This issue persists even when we get the events from the parser directly rather than `TextMergeStream`:

````diff
-let iter = TextMergeStream::new(parser);

-let events = iter.collect::<Vec<_>>();
+let events = parser.collect::<Vec<_>>();
````

Looking at the [default options](https://github.com/Byron/pulldown-cmark-to-cmark/blob/bf34a3cac68e6f82a24ee3d44224a9e2ef2bcd0d/src/lib.rs#L180), we find

````rust
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
````

Specifically,

````rust
increment_ordered_list_bullets: false,
list_token: '*',
````

In our case we use `-` and we do increment bullets. So let's do that.

````rust
increment_ordered_list_bullets: true,
list_token: '-',
````

````rust
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
````

Let's try again.

2025-09-23 Wk 39 Tue - 04:02 +03:00

````sh
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
````

Awesome!

Now the other issue.

````sh
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
````

They're dashes as per our conventions!
