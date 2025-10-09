---
parent: '[[000 Note Repo Migration Sept 8]]'
spawned_by: '[[001 Parse through all notes and classify as core - peripheral - partial]]'
context_type: investigation
status: done
---

Parent: [000 Note Repo Migration Sept 8](../000%20Note%20Repo%20Migration%20Sept%208.md)

Spawned by: [001 Parse through all notes and classify as core - peripheral - partial](../tasks/001%20Parse%20through%20all%20notes%20and%20classify%20as%20core%20-%20peripheral%20-%20partial.md)

Spawned in: [<a name="spawn-invst-db4910" />^spawn-invst-db4910](../tasks/001%20Parse%20through%20all%20notes%20and%20classify%20as%20core%20-%20peripheral%20-%20partial.md#spawn-invst-db4910)

# 1 Journal

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt004_old_format_record_list ~/src/cloned/gh/LanHikari22/lan-setup-notes

# out (relevent)
[nothing]
````

There should be many.

2025-09-16 Wk 38 Tue - 22:55 +03:00

Maybe let's rename form `SubH2` to just `content`. This breaks that assumption:

````
H1("1 What is this?"), SubH2([Start(Paragraph), Text(Borrowed("To protect against broken links, anywhere notes are referenced externally will be recorded here.")), End(Paragraph)]), H1("2 Journal"),
````

If it does not handle H1, or H2, it just assumes the third category and starts collecting.

So we are able to see grouped events but not relevant grouped events:

````rust
log::trace!("{relevant_grouped_events:?}");
````

These show nothing.

2025-09-16 Wk 38 Tue - 23:52 +03:00

Was just a matter of trimming in

````rust
!OLD_FORMAT_HEADINGS.contains(&strip_autonumbered_sections(&heading1).trim())
````

and now we have relevant grouped events.

2025-09-16 Wk 38 Tue - 23:55 +03:00

Now this shows nothing

````rust
log::trace!("{old_format_entries:?}");
````

2025-09-17 Wk 38 Wed - 00:15 +03:00

Turned the Option errors into Result and temporarily replaced `?` with unwrap to inspect.

````
thread 'main' panicked at src/cluster_note.rs:652:89:
called `Result::unwrap()` on an `Err` value: InvalidEntryType(InvalidType("4 HowTos"))
````

Okay, we just haven't stripped the heading numbers.

````
thread 'main' panicked at src/cluster_note.rs:652:89:
called `Result::unwrap()` on an `Err` value: InvalidEntryType(InvalidType(" HowTos"))
````

And also didn't trim it!

2025-09-17 Wk 38 Wed - 00:32 +03:00

Now `expt004` gives us old format event information!

`000 Create script to analyze expedition 33 golgra fight and count turns.md`  though says `5.1` has no spawn metadata and no linkables.

There aren't linkables, but it does have spawn.

2025-09-17 Wk 38 Wed - 00:55 +03:00

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt004_old_format_record_list ~/src/cloned/gh/LanHikari22/lan-setup-notes ~/src/cloned/gh/LanHikari22/lan-setup-notes/lan/topics/gaming/tasks/2025/000\ Create\ script\ to\ analyze\ expedition\ 33\ golgra\ fight\ and\ count\ turns.md | less
````

The event we're interested in for `spawned` is

````
ObsidianLinkItem { links: [], event: Text(Boxed("From [[#^spawn-howto-ba135a]] in [[#3.1 Create Script to press keys at exact intervals]]")) }
````

But yet it says there are no links in it... even though it created the object.

(update)
There's also a weird event

````
ObsidianLinkItem { links: [], event: Text(Inlined(InlineStr { inner: [91, 120, 93, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0], len: 3 })) }
````

2025-09-17 Wk 38 Wed - 01:52 +03:00

You also can't find it when you just look through the events with `expt000`.

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt000_parse_single_pulldown_cmark ~/src/cloned/gh/LanHikari22/lan-setup-notes/lan/topics/gaming/tasks/2025/000\ Create\ script\ to\ analyze\ expedition\ 33\ golgra\ fight\ and\ count\ turns.md | less
````

2025-09-17 Wk 38 Wed - 02:47 +03:00

````sh
python3 -c "import checkpipe as pipe; l = [91, 120, 93, 0] | pipe.OfIter[int].map(lambda n: chr(n)) | pipe.OfIter[str].to_list(); print(''.join(l))"

# out
[x]
````

There are multiple events like that:

````
Event Text(Boxed("[x]"))
````

We're dealing with `&Event` instead of `Event`...

2025-09-17 Wk 38 Wed - 02:56 +03:00

We were able to reproduce the behavior in `expt000` by using

````rust
#[derive(Debug)]
pub struct Test<'a> {
	event: Event<'a>
}

println!("Event {:?}", Test {event: event.clone()});
````

Instead of

````rust
println!("Event {event:?}");
````

(/update)

2025-09-17 Wk 38 Wed - 01:08 +03:00

`parse_multiple_obsidian_links` adopts the strategy of trying to parse for a valid token at each character. But the problem is it expects a single token, and not just the beginning of a character stream.

Let's properly tokenize.

2025-09-17 Wk 38 Wed - 01:30 +03:00

We're tokenizing, and now extract has proper error type. Let's check what we get.

````
thread 'main' panicked at src/bin/expt004_old_format_record_list.rs:22:22:
Failed to extract obsidian links: LinkExtractError(NoBracketsFound("8710732505532484849327911032118105115117971083210511011211711644321121141011151153211511110910132107101121115"))
````

Oh no.

````rust
let stripped = s
	.as_bytes()
	[..end_idx]
	.iter()
	.join("");
````

This did not go as planned. It's just turning the numbers themselves into strings!

2025-09-17 Wk 38 Wed - 01:39 +03:00

Correcting to this.

````rust
let stripped = s
	.chars()
	.take(end_idx + "]]".len())
	.join("");
````

We want to also include the `]]`.

````
thread 'main' panicked at src/bin/expt004_old_format_record_list.rs:22:22:
Failed to extract obsidian links: LinkExtractError(NoBracketsFound("Wk 27 001 On visual input, press some keys]]"))
````

And don't forget to add the `[[` back...

````
thread 'main' panicked at src/bin/expt004_old_format_record_list.rs:22:22:
Failed to extract obsidian links: LinkExtractError(MustHaveZeroOrOneHash(2))
````

2025-09-17 Wk 38 Wed - 01:46 +03:00

````
thread 'main' panicked at src/bin/expt004_old_format_record_list.rs:22:22:
Failed to extract obsidian links: LinkExtractError(MustHaveZeroOrOneHash(2, "#5.1 Listen for key events in Linux in Python3[[#5.1 Listen for key events in Linux in Python3]]"))
````

A trace shows that we're tokenizing fine:

````
[2025-09-17 01:48:28 TRACE src/common.rs:587] tokens: ["[[#5.1 Listen for key events in Linux in Python3]]"]
````

So the `ObsidianLink` parser is failing.

````rust
let remaining_s = s.replace("[[", "").replace("]]", s);
````

Yeah definitely don't replace `"]]"` with `s`. Bad typo.

2025-09-17 Wk 38 Wed - 01:53 +03:00

There's still the issue with events that have 0 links appearing like

````
ObsidianLinkItem { links: [], event: Text(Borrowed("2025-08-27 Wk 35 Wed - 18:10")) }
````

(update)

We can return an empty vec in that case.

<details>
<summary>deprecated</summary>

2025-09-17 Wk 38 Wed - 01:59 +03:00

Deprecated the following, since we can just return an empty vec.

We added a new error to `extract_obsidian_md_links`,

````rust
if links.is_empty() {
	return Err(ExtractOBsidianMdLinksError::NoLinksFound)
}
````

This is a failure that `expt004` should be able to handle.

</details>

(/update)

2025-09-17 Wk 38 Wed - 02:01 +03:00

Spawn metadata shows up now!

2025-09-17 Wk 38 Wed - 03:10 +03:00

We need to check that we're parsing linkables correctly.

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt004_old_format_record_list ~/src/cloned/gh/LanHikari22/lan-setup-notes | less

# out (relevant)
path "/home/lan/src/cloned/gh/LanHikari22/lan-setup-notes/lan/topics/tooling/obsidian/tasks/2025/004 Fix obsidian export to support internal links.md" has 24 records
[2025-09-17 03:12:04 TRACE src/bin/expt004_old_format_record_list.rs:33] record "2.1 Capture details on the broken links problem" of Task has 322 events.
[2025-09-17 03:12:04 TRACE src/bin/expt004_old_format_record_list.rs:34]        It has 7 linkables, 14 links, and 1 spawn items.
````

That has 7 headings, so it correctly has 7 linkables. No block identifiers.

2025-09-17 Wk 38 Wed - 03:17 +03:00

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin expt004_old_format_record_list ~/src/cloned/gh/LanHikari22/lan-setup-notes ~/src/cloned/gh/LanHikari22/lan-setup-notes/lan/topics/tooling/obsidian/tasks/2025/004\ Fix\ obsidian\ export\ to\ support\ internal\ links.md | less

# out (relevant)
[2025-09-17 03:17:41 TRACE src/bin/expt004_old_format_record_list.rs:38] linkables: [ObsidianLinkableItem { item_data: Heading(H3, "2.1.1 Broken file-internal link"), event: Start(Heading { level: H3, id: None, classes: [], attrs: [] }) }, ObsidianLinkableItem { item_data: Heading(H3, "2.1.2 Broken Embedded text links"), event: Start(Heading { level: H3, id: None, classes: [], attrs: [] }) }, ObsidianLinkableItem { item_data: Heading(H3, "2.1.3 Comparison with how I made valid README internal file links"), event: Start(Heading { level: H3, id: None, classes: [], attrs: [] }) }, ObsidianLinkableItem { item_data: Heading(H3, "2.1.4 Case 1: Basic mapping and filtering"), event: Start(Heading { level: H3, id: None, classes: [], attrs: [] }) }, ObsidianLinkableItem { item_data: Heading(H3, "2.1.5 Beep"), event: Start(Heading { level: H3, id: None, classes: [], attrs: [] }) }, ObsidianLinkableItem { item_data: Heading(H3, "2.1.6 Beep"), event: Start(Heading { level: H3, id: None, classes: [], attrs: [] }) }, ObsidianLinkableItem { item_data: Heading(H3, "2.1.7 Test Some description and things"), event: Start(Heading { level: H3, id: None, classes: [], attrs: [] }) }]
````

Looks good!
