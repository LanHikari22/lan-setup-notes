---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[001 pulldown cmark to cmark escapes first obsidian tag on writeback]]"
context_type: task
status: todo
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


