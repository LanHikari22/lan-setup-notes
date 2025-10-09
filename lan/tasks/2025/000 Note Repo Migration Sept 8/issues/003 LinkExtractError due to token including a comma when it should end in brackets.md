---
parent: '[[000 Note Repo Migration Sept 8]]'
spawned_by: '[[013 Change all delta-trace old format notes into note clusters without applying link fixing]]'
context_type: issue
status: done
---

Parent: [000 Note Repo Migration Sept 8](../000%20Note%20Repo%20Migration%20Sept%208.md)

Spawned by: [013 Change all delta-trace old format notes into note clusters without applying link fixing](../tasks/013%20Change%20all%20delta-trace%20old%20format%20notes%20into%20note%20clusters%20without%20applying%20link%20fixing.md)

Spawned in: [<a name="spawn-issue-b66790" />^spawn-issue-b66790](../tasks/013%20Change%20all%20delta-trace%20old%20format%20notes%20into%20note%20clusters%20without%20applying%20link%20fixing.md#spawn-issue-b66790)

# 1 Journal

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --bin app -- writeback /home/lan/src/cloned/gh/deltatraced/delta-trace/
````

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --release --bin app -- extract_old_format_records /home/lan/src/cloned/gh/deltatraced/delta-trace/


# out (error, relevant)
[2025-09-30T07:19:55Z INFO  app] processing "/home/lan/src/cloned/gh/deltatraced/delta-trace/lan/topics/study/books/math/2025/001 Probability - Theory and Examples/entries/2025/000 Starting out Probability Theory and Examples.md"

thread 'main' panicked at src/bin/app.rs:98:64:
Could not process links: LinkExtractError(NoBracketsFound("[[018 σ-additive|σ-additivity]], "))
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
````

2025-09-30 Wk 40 Tue - 10:27 +03:00

When parsing links we expected

````diff
// in impl FromStr for ObsidianLink
if !s.starts_with("[[") || !s.ends_with("]]") {
-	return Err(ObsidianLinkParseError::NoBracketsFound(s.to_owned()));
+   return Err(ObsidianLinkParseError::NotEnclosedInBrackets(s.to_owned()));
}
````

But in this case `,`  came along. This error name should also be clarified. There are brackets, but it is true that it's not enclosed in them.

2025-09-30 Wk 40 Tue - 10:46 +03:00

Adding some traces,

````rust
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
````

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo run --release --bin app -- -vv  extract_old_format_records /home/lan/src/cloned/gh/deltatraced/delta-trace/

# out (error, relevant)
[2025-09-30T07:45:43Z TRACE migration_rs::common] s: 018 σ-additive|σ-additivity]], which means that for the corresponding set, when you take the measure of the union of two sets, it corresponds to adding the measures of each:
[2025-09-30T07:45:43Z TRACE migration_rs::common] stripped: 018 σ-additive|σ-additivity]], 

thread 'main' panicked at src/bin/app.rs:98:64:
Could not process links: LinkExtractError(NotEnclosedInBrackets("[[018 σ-additive|σ-additivity]], "))
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
````

2025-10-06 Wk 41 Mon - 04:51 +03:00

So we're trying to understand why the comma with `[[018 σ-additive|σ-additivity]],`.

The logic that tokenizes is in `fn parse_multiple_obsidian_links`

````rust
// in fn parse_multiple_obsidian_links
let end_idx = s_split
	.find("]]")
	.ok_or(ObsidianLinkParseAssertError::FilteredForEndBracketsMustBeFound)?;

let stripped = s_split.chars().take(end_idx + "]]".len()).join("");
````

2025-10-06 Wk 41 Mon - 04:59 +03:00

I thought Maybe this logic is flawed given that there's unicode there, but the example for `find` includes unicode!

Also curiously [doc.rs str](https://docs.rs/rustc-std-workspace-std/latest/std/str/index.html) module doesn't include this find, although in the code on my machine it's somewhere under a str module. But not std, instead rustlib?

[docs.rs rustLib](https://docs.rs/rustLib/latest/rustLib/) seems to be just a template. In my machine, the code is found in `/home/lan/.rustup/toolchains/stable-x86_64-unknown-linux-gnu/lib/rustlib/src/rust/library/core/src/str/mod.rs`.

2025-10-06 Wk 41 Mon - 05:14 +03:00

Okay it was in the core library [docs.rs core](https://doc.rust-lang.org/stable/core/) as [fn find](https://doc.rust-lang.org/stable/core/primitive.str.html#method.find)

 > 
 > Returns the byte index of the first character of this string slice that matches the pattern.

Let's do some tests.

2025-10-06 Wk 41 Mon - 05:21 +03:00

Checked `/home/lan/src/cloned/gh/LanHikari22/bn_repo_editor` for unit testing template in-file

2025-10-06 Wk 41 Mon - 05:25 +03:00

Our unit under test now is `fn get_obsidian_link_token_from_open_bracket_split` which is the significant map logic from `fn parse_multiple_obsidian_links`

2025-10-06 Wk 41 Mon - 06:12 +03:00

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo test --lib

# out (error, relevant)
---- common::tests::test_get_obsidian_link_token_from_open_bracket_split stdout ----

<given>
[[Löwe 老虎 Léopard Gepardi]],
</given>


<expected>
[[Löwe 老虎 Léopard Gepardi]]
</expected>


<actual>
[[Löwe 老虎 Léopard Gepardi]],
</actual>


thread 'common::tests::test_get_obsidian_link_token_from_open_bracket_split' panicked at src/common.rs:1025:25:
case-003: Output is not as expected
````

So this issue reproduces even for the unicode examples they had.

2025-10-06 Wk 41 Mon - 06:19 +03:00

````rust
// in fn get_obsidian_link_token_from_open_bracket_split
pub fn get_obsidian_link_token_from_open_bracket_split(
    s_split: &str,
) -> Result<String, ObsidianLinkParseError> {
    let end_idx = s_split
        .find("]]")
        .ok_or(ObsidianLinkParseAssertError::FilteredForEndBracketsMustBeFound)?;

    let stripped = s_split.chars().take(end_idx + "]]".len()).join("");

    log::trace!("end_idx: {end_idx}");
    log::trace!("s: {s_split}");
    log::trace!("stripped: {stripped}");

    // It will already include ]], but we have to put the [[ back
    Ok(format!("[[{stripped}"))
}
````

````sh
[2025-10-06T03:18:37Z TRACE migration_rs::common] end_idx: 4
[2025-10-06T03:18:37Z TRACE migration_rs::common] s: beep]]
[2025-10-06T03:18:37Z TRACE migration_rs::common] stripped: beep]]

[2025-10-06T03:18:37Z TRACE migration_rs::common] end_idx: 29
[2025-10-06T03:18:37Z TRACE migration_rs::common] s: Löwe 老虎 Léopard Gepardi]],
[2025-10-06T03:18:37Z TRACE migration_rs::common] stripped: Löwe 老虎 Léopard Gepardi]],
````

Assuming each unicode character is 2 bytes here, the end index of 29 should be 27, this would be at the start of the pattern `]]`.  29 is the comma after. Why is it overcounting by 2?

2025-10-06 Wk 41 Mon - 06:27 +03:00

````python
>>> s
'Löwe 老虎 Léopard Gepardi'
>>> n = 1; s[n], hex(ord(s[n]))
('ö', '0xf6')
>>> n = 9; s[n], hex(ord(s[n]))
('é', '0xe9')
>>> n = 5; s[n], hex(ord(s[n]))
('老', '0x8001')
>>> n = 6; s[n], hex(ord(s[n]))
('虎', '0x864e')
````

So only two characters are 2-byte according to python, but then it would mean that we expect `[[` to be at byte index 25, and yet we're getting a reading of 29. That's overshooting by 4.

2025-10-06 Wk 41 Mon - 06:37 +03:00

It fails even with just `[[ö]]` and `[[老]]`

````sh
[2025-10-06T03:37:25Z TRACE migration_rs::common] end_idx: 2
[2025-10-06T03:37:25Z TRACE migration_rs::common] s: ö]],
[2025-10-06T03:37:25Z TRACE migration_rs::common] stripped: ö]],

[2025-10-06T03:36:58Z TRACE migration_rs::common] end_idx: 3
[2025-10-06T03:36:58Z TRACE migration_rs::common] s: 老]],
[2025-10-06T03:36:58Z TRACE migration_rs::common] stripped: 老]],
````

2025-10-06 Wk 41 Mon - 06:44 +03:00

For the traces in

````rust
pub fn get_obsidian_link_token_from_open_bracket_split(
    s_split: &str,
) -> Result<String, ObsidianLinkParseError> {
    let end_idx = s_split
        .find("]]")
        .ok_or(ObsidianLinkParseAssertError::FilteredForEndBracketsMustBeFound)?;

    let stripped = s_split.chars().take(end_idx + "]]".len()).join("");

    for (i, c) in s_split.char_indices() {
        log::trace!("{i} {c}");
    }

    log::trace!("end_idx: {end_idx}");
    log::trace!("s: {s_split}");
    log::trace!("stripped: {stripped}");

    // It will already include ]], but we have to put the [[ back
    Ok(format!("[[{stripped}"))
}
````

````
[2025-10-06T03:44:25Z TRACE migration_rs::common] 0 b
[2025-10-06T03:44:25Z TRACE migration_rs::common] 1 e
[2025-10-06T03:44:25Z TRACE migration_rs::common] 2 e
[2025-10-06T03:44:25Z TRACE migration_rs::common] 3 p
[2025-10-06T03:44:25Z TRACE migration_rs::common] 4 ]
[2025-10-06T03:44:25Z TRACE migration_rs::common] 5 ]
[2025-10-06T03:44:25Z TRACE migration_rs::common] end_idx: 4
[2025-10-06T03:44:25Z TRACE migration_rs::common] s: beep]]
[2025-10-06T03:44:25Z TRACE migration_rs::common] stripped: beep]]

[2025-10-06T03:44:25Z TRACE migration_rs::common] 0 ö
[2025-10-06T03:44:25Z TRACE migration_rs::common] 2 ]
[2025-10-06T03:44:25Z TRACE migration_rs::common] 3 ]
[2025-10-06T03:44:25Z TRACE migration_rs::common] 4 ,
[2025-10-06T03:44:25Z TRACE migration_rs::common] end_idx: 2
[2025-10-06T03:44:25Z TRACE migration_rs::common] s: ö]],
[2025-10-06T03:44:25Z TRACE migration_rs::common] stripped: ö]],

[2025-10-06T03:46:31Z TRACE migration_rs::common] 0 老
[2025-10-06T03:46:31Z TRACE migration_rs::common] 3 ]
[2025-10-06T03:46:31Z TRACE migration_rs::common] 4 ]
[2025-10-06T03:46:31Z TRACE migration_rs::common] 5 ,
[2025-10-06T03:46:31Z TRACE migration_rs::common] end_idx: 3
[2025-10-06T03:46:31Z TRACE migration_rs::common] s: 老]],
[2025-10-06T03:46:31Z TRACE migration_rs::common] stripped: 老]],
````

[fn char_indices](https://doc.rust-lang.org/stable/core/primitive.str.html#method.char_indices) indicates that it is using UTF-8 encoding.

We can confirm the character lengths searching [utf-8 chartable](https://www.utf8-chartable.de/) using the hexes identified in python

From [utf-8 chartable start=246 (0xF6)](https://www.utf8-chartable.de/unicode-utf8-table.pl?start=246),

|Unicode  <br>code point|character|UTF-8  <br>(hex.)|name|
|-------------------|---------|-------------|----|
|U+00F6|ö|c3 b6|LATIN SMALL LETTER O WITH DIAERESIS|

And from [utf-8 chartable start=32769 (0x8001)](https://www.utf8-chartable.de/unicode-utf8-table.pl?start=32769),

|Unicode  <br>code point|character|UTF-8  <br>(hex.)|name|
|-------------------|---------|-------------|----|
|U+8001|老|e8 80 81||

2025-10-06 Wk 41 Mon - 07:40 +03:00

Anyway the real problem is using a byte index in a chars iterator. The latter doesn't care about the underlying UTF-8 encoding details, and should NOT be given byte indices!

````rust
// in fn get_obsidian_link_token_from_open_bracket_split
let end_idx = s_split
	.find("]]")
	.ok_or(ObsidianLinkParseAssertError::FilteredForEndBracketsMustBeFound)?;

let stripped = s_split.chars().take(end_idx + "]]".len()).join("");
````

2025-10-06 Wk 41 Mon - 08:19 +03:00

Replacing

````rust
// in fn get_obsidian_link_token_from_open_bracket_split
let end_idx = s_split
	.find("]]")
	.ok_or(ObsidianLinkParseAssertError::FilteredForEndBracketsMustBeFound)?;
````

With

````rust
// in fn get_obsidian_link_token_from_open_bracket_split
let end_idx = s_split
	.chars()
	.enumerate()
	.chunks(2)
	.into_iter()
	.map(|chunk| {
		let (is, cs): (Vec<usize>, Vec<char>) = chunk
			.into_iter()
			.unzip();

		let s = cs
			.into_iter()
			.join("");
		
		Some((is[0], s))
	})
	.flatten()
	.filter(|(_, s)| s == "]]")
	.next()
	.ok_or(ObsidianLinkParseAssertError::FilteredForEndBracketsMustBeFound)?
	.0;
````

Yields

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo test --lib

# out (error, relevant)
thread 'common::tests::test_get_obsidian_link_token_from_open_bracket_split' panicked at src/common.rs:1081:26:
case-002: Failed to tokenize: AssertError(FilteredForEndBracketsMustBeFound)
````

This is also a flawed idea since you don't know whether the pattern exists at an odd or even index.  `a]]` would give the chunks `a] ]` and not `a ]]`.

2025-10-06 Wk 41 Mon - 08:34 +03:00

This passes the tests:

````rust
// in fn get_obsidian_link_token_from_open_bracket_split
let s_split_chars = s_split.chars().collect_vec();

let end_idx = (0..s_split_chars.len())
	.find(|i| {
		if s_split_chars.len() <= i+1 {
			return false;
		}

		let s = s_split_chars[*i..i+2]
			.iter()
			.join("");

		s == "]]"
	})
	.ok_or(ObsidianLinkParseAssertError::FilteredForEndBracketsMustBeFound)?;
````

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
cargo test --lib

# out
   Compiling migration_rs v0.1.0 (/home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration)
    Finished `test` profile [unoptimized + debuginfo] target(s) in 1.78s
     Running unittests src/lib.rs (target/debug/deps/migration_rs-9afd18e7dbf7e5fe)

running 1 test
test common::tests::test_get_obsidian_link_token_from_open_bracket_split ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s
````
