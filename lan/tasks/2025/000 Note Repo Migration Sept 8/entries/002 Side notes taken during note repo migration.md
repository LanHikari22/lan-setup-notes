---
parent: '[[000 Note Repo Migration Sept 8]]'
spawned_by: '[[000 Note Repo Migration Sept 8]]'
context_type: entry
---

Parent: [000 Note Repo Migration Sept 8](../000%20Note%20Repo%20Migration%20Sept%208.md)

Spawned by: [000 Note Repo Migration Sept 8](../000%20Note%20Repo%20Migration%20Sept%208.md)

Spawned in: [<a name="spawn-entry-92491b" />^spawn-entry-92491b](../000%20Note%20Repo%20Migration%20Sept%208.md#spawn-entry-92491b)

# 1 Journal

2025-10-06 Wk 41 Mon - 05:42 +03:00

During [003 LinkExtractError due to token including a comma when it should end in brackets](../issues/003%20LinkExtractError%20due%20to%20token%20including%20a%20comma%20when%20it%20should%20end%20in%20brackets.md),

![Pasted image 20251006054220.png](../../../../../attachments/Pasted%20image%2020251006054220.png)

Rust has an interesting syntax for mapping struct names to local variable names

So I could do

````rust
TestData::Identical { name: name2, given } => {...}
````

To use `given` as it is named or use `name2` instead of `name` as defined in the struct.

2025-10-06 Wk 41 Mon - 13:43 +03:00

During [014 impl get_note_old_format_entries_from_content](../tasks/014%20impl%20get_note_old_format_entries_from_content.md),

````rust
// in fn get_note_old_format_entries_from_content
#[derive(Debug)]
enum Grouped<'a> {
	H1(&'a str),
	H2(&'a str),
	Content(Vec<&'a str>),
}
````

![Pasted image 20251006134411.png](../../../../../attachments/Pasted%20image%2020251006134411.png)

![Pasted image 20251006134357.png](../../../../../attachments/Pasted%20image%2020251006134357.png)

When using the `Fill match arms` option in vscode, it gives the `Grouped::Content` data a default parameter name of `items`, likely because it's a vec
