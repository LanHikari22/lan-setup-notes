---
status: todo
---
2025-09-04 Wk 36 Thu - 10:57# 1 Objective

# 1 Journal

2025-08-13 Wk 33 Wed - 10:01

First we need to reproduce this issue. 

Spawn [[#3.1 Capture details on the broken links problem]] ^spawn-task-a5bc4a

2025-08-13 Wk 33 Wed - 14:21

Spawn [[#3.4 File issue about internal link behavior]] ^spawn-task-0ca517

# 2 Tasks

## 2.1 Capture details on the broken links problem

- [ ] 

### 2.1.1 Broken file-internal link

2025-08-13 Wk 33 Wed - 10:01

From [[#^spawn-task-a5bc4a]] in [[#2 Journal]]

Basically the links used here, once we export this repository, we will not be able to click.

In the [raw webview export of this note](https://raw.githubusercontent.com/LanHikari22/lan-setup-notes/refs/heads/webview/lan/topics/tooling/obsidian/tasks/2025/004%20Fix%20obsidian%20export%20to%20support%20internal%20links.md) (this always updates), 

```
From [^spawn-task-a5bc4a](004%20Fix%20obsidian%20export%20to%20support%20internal%20links.md#spawn-task-a5bc4a).
```

### 2.1.2 Broken Embedded text links

Also, embedded note content does not work like

---

!!!

> [!NOTE] WARNING
> INCORRECT FULL NOTE INSERTION START


```
![[003 Making obsidian repositories appear nicer in browser#^ebe248]]
```

> [!NOTE] WARNING
> INCORRECT FULL NOTE INSERTION END

This should somehow display the snippet I selected from the other note, but currently it does not.

I should see:

![[Pasted image 20250813105702.png]]

What I see:

The entire note being embedded:

![[Pasted image 20250813102841.png]]

In obsidian, the link itself looks like

![[Pasted image 20250813102914.png]]

It's a generated code for a section I wanted to pull out from the other note.

In the [raw webview export of this note](https://raw.githubusercontent.com/LanHikari22/lan-setup-notes/refs/heads/webview/lan/topics/tooling/obsidian/tasks/2025/004%20Fix%20obsidian%20export%20to%20support%20internal%20links.md) (this always updates), 

```
Also, embedded note content does not work like

# 1 Objective

As is, repositories will not load image,s embedded links nor internal links. So it makes the obsidian view diverge a lot from the web view. Since we provide URL links to notes, it would be good for the views to be more compatible.

# 2 Journal

## 2.1 Outstanding issues
[...]
```

It just copied the entire note text.

### 2.1.3 Comparison with how I made valid README internal file links

2025-08-13 Wk 33 Wed - 10:13

Let's take my [checkpipe README](https://github.com/LanHikari22/checkpipe/blob/4373f01a6c17cd524b13723373f6de7897f2520e/README.md) ([raw](https://raw.githubusercontent.com/LanHikari22/checkpipe/4373f01a6c17cd524b13723373f6de7897f2520e/README.md)) for example.

We create a table of contents reference

```
[Case 1: Basic mapping and filtering](#case-1-basic-mapping-and-filtering)
```

for 

```
### Case 1: Basic mapping and filtering
```


Let's try to replicate what [current obsidian-export](https://github.com/zoni/obsidian-export/tree/ba5beecf4278f38b54e732d8a86ff2615af87b48) does for this. 

[[#3.1.4 Case 1 Basic mapping and filtering]]

### 2.1.4 Case 1: Basic mapping and filtering


In the [raw webview export of this note](https://raw.githubusercontent.com/LanHikari22/lan-setup-notes/refs/heads/webview/lan/topics/tooling/obsidian/tasks/2025/004%20Fix%20obsidian%20export%20to%20support%20internal%20links.md) (this always updates), 

```
[3.1.4 Case 1 Basic mapping and filtering](004%20Fix%20obsidian%20export%20to%20support%20internal%20links.md#3-1-4-case-1-basic-mapping-and-filtering)
```

Let's try different variants of this and see if any of them work.
[Original](004%20Fix%20obsidian%20export%20to%20support%20internal%20links.md#3-1-4-case-1-basic-mapping-and-filtering)
[Heading only](#3-1-1-case-1-basic-mapping-and-filtering)

The above links don't work in obsidian either.

We know the below beep link works, even in obsidian following, 

2025-08-13 Wk 33 Wed - 11:55

We can also inspect the html of the markdown rendered and see that it passes extra user and project information:

![[Pasted image 20250813115610.png]]

This is the heading HTML:

![[Pasted image 20250813115958.png]]


Now compare that with the HTML for the Case 1 in my [checkpipe README](https://github.com/LanHikari22/checkpipe/blob/4373f01a6c17cd524b13723373f6de7897f2520e/README.md) which we know works in Github:

![[Pasted image 20250813120220.png]]

This is the heading HTML:

![[Pasted image 20250813121025.png]]

So what gives here? Why is it adding this information in this case but not the previous one?

Spawn [[#6.2 Why is github markdown rendering adding full repo information for some internal markdown links but not others?]] ^spawn-invst-767a80

Let's  modify the HTML and remove the extra user/repo/file information. Change 

```html
<a href="/LanHikari22/lan-setup-notes/blob/webview/lan/topics/tooling/obsidian/tasks/2025/004%20Fix%20obsidian%20export%20to%20support%20internal%20links.md#3-1-4-case-1-basic-mapping-and-filtering">3.1.4 Case 1 Basic mapping and filtering</a>
```

to

```html
<a href="#3-1-4-case-1-basic-mapping-and-filtering">3.1.4 Case 1 Basic mapping and filtering</a>
```

2025-08-13 Wk 33 Wed - 12:07

No change.

2025-08-13 Wk 33 Wed - 12:18

Actually when we click on the heading link in github, we see

```
#314-case-1-basic-mapping-and-filtering
```

So this should be the correct heading? `3.1.4` become `314` instead of `3-1-4`. 

Testing the change

```diff
-<a href="/LanHikari22/lan-setup-notes/blob/webview/lan/topics/tooling/obsidian/tasks/2025/004%20Fix%20obsidian%20export%20to%20support%20internal%20links.md#3-1-4-case-1-basic-mapping-and-filtering">3.1.4 Case 1 Basic mapping and filtering</a>
+<a href="/LanHikari22/lan-setup-notes/blob/webview/lan/topics/tooling/obsidian/tasks/2025/004%20Fix%20obsidian%20export%20to%20support%20internal%20links.md#314-case-1-basic-mapping-and-filtering">3.1.4 Case 1 Basic mapping and filtering</a>
```

It works!


---

[beep](#beep)
- Works in obsidian (so long as heading autonumbering didn't kick in yet!)
### 2.1.5 Beep

---

[316-beep](#316-beep)
- Doesn't work in obsidian. I change the header below to an H1 for a reproducible example. But still it won't follow.
- Even if I change it to `316-beep`, which we know seems to be recognizable within github, it will not work in obsidian.
- Converts to `<a href="#316-beep">316-beep</a>` in github html and does redirect fine.
### 2.1.6 Beep

---

[Test1](#Test-Some-description-and-things)
### 2.1.7 Test Some description and things

2025-08-13 Wk 33 Wed - 11:08

Spawn [[#6.1 What does the markdown standard say about internal page links?]] ^spawn-invst-d8b7ce

2025-08-13 Wk 33 Wed - 12:45

Spawn [[#3.2 Attempting to reproduce header jump behavior in local html]] ^spawn-task-884959

2025-08-13 Wk 33 Wed - 13:58

Okay so we understand the issue with heading links. The automatic header numbers shouldn't be `N.M.L` they should be `nml`.

Next we need to figure out what to do about `[[#^caret]]` links.

Spawn [[#3.3 Look into broken caret links]] ^spawn-task-c588e2

## 2.2 Attempting to reproduce header jump behavior in local html

- [ ] Skipped, the header seek behavior seems to be part of a github website impl in some way

From [[#^spawn-task-884959]] in [[#3.1.7 Test Some description and things]]

2025-08-13 Wk 33 Wed - 12:45

I'm trying to reproduce some of this in basic HTML in [repro.html](https://github.com/LanHikari22/lan-exp-scripts/blob/main/files/2025/weekly/Wk33-000-html-markdown-fragment-repro/repro.html).

Spawn [[#5.2 Trace class in heading html definition]] ^spawn-howto-e804cf

2025-08-13 Wk 33 Wed - 13:39

Let's try to get the raw html as a file and strip it down and see we see if we can keep reproducing correct header jump behavior for [raw checkpipe README](https://github.com/LanHikari22/checkpipe/blob/4373f01a6c17cd524b13723373f6de7897f2520e/README.md#case-1-basic-mapping-and-filtering)

```sh
wget https://github.com/LanHikari22/checkpipe/blob/4373f01a6c17cd524b13723373f6de7897f2520e/README.md\#case-1-basic-mapping-and-filtering -O reconstruct.html
```

No good, nothing will render without github scripts running.
### 2.2.1 Skipped

## 2.3 Look into broken caret links

- [ ] 

From [[#^spawn-task-c588e2]] in [[#3.1 Capture details on the broken links problem]].

2025-08-13 Wk 33 Wed - 14:01

So the above spawn caret link will generate the html

```html
<a href="/LanHikari22/lan-setup-notes/blob/webview/lan/topics/tooling/obsidian/tasks/2025/004%20Fix%20obsidian%20export%20to%20support%20internal%20links.md#spawn-task-c588e2">^spawn-task-c588e2</a>
```

And the line with `^spawn-task-c588e2` simply has no heading or anything, so it can't be jumped to. 

```html
<p dir="auto" style="background-color: transparent;">Spawn <a href="/LanHikari22/lan-setup-notes/blob/webview/lan/topics/tooling/obsidian/tasks/2025/004%20Fix%20obsidian%20export%20to%20support%20internal%20links.md#3-3-look-into-broken-caret-links">3.3 Look into broken caret links</a> ^spawn-task-c588e2</p>
```

How could we extend `obsidian-export` such that these links would be supported?

Here's the [obsidian docs on heading links](https://help.obsidian.md/links#Link+to+a+heading+in+a+note). 

I didn't know it supported nested headings `Note#H2#H3`...

The caret link featured is explained in [obsidian docs on linking to a block](https://help.obsidian.md/links#Link+to+a+block+in+a+note).

They specifically flag interoperability issues there...

![[Pasted image 20250813141416.png]]

2025-08-13 Wk 33 Wed - 14:19

One suggestion here is to put a header wherever a block is declared `^someblock` that looks like `# Block: someblock` and then  convert all block link references to references to this header. The header itself must have a heading level of the last heading in the document + 1.

## 2.4 File issue about internal link behavior

- [ ] 

From [[#^spawn-task-0ca517]] in [[#2 Journal]].

2025-08-13 Wk 33 Wed - 14:28

We've identified export issues with internal links that begin with automatic numbers using the [Number Headings](https://github.com/onlyafly/number-headings-obsidian) ([obsidian](obsidian://show-plugin?id=number-headings-obsidian)) plugin in [[#3.1 Capture details on the broken links problem]].

We've identified that embedding notes using block notes instead embeds the entire repository in [[#3.1 Capture details on the broken links problem]]. This has been filed in [#112](https://github.com/zoni/obsidian-export/issues/112).

We've also identified that block notes are not supported and suggested to use automatic block note headings as a resolution in [[#3.3 Look into broken caret links]].

We should file issues for each of those problems.

But first let's try to look if anyone filed anything similar.

[#33](https://github.com/zoni/obsidian-export/issues/33) is related. Here the issue is that if the embedded reference has parentheses, we end up getting the whole page stuff.

[#32](https://github.com/zoni/obsidian-export/issues/32) explains footnote behavior which is also not supported, and the author mentions it's not in CommonMark spec...

So I need to file an issue about the numbered headers breaking on export. This is already partially supported, but the numbering is what breaks it.

I also need to file an issue about my caret suggestion to be supported under some option. And that this could relate to resolving [#112](https://github.com/zoni/obsidian-export/issues/112) at least in the use case of using this option since it would convert it to a header.

2025-08-13 Wk 33 Wed - 14:57

Filed issue [#370](https://github.com/zoni/obsidian-export/issues/370) for numbered headers.

2025-08-13 Wk 33 Wed - 15:26

Filed issue [#371](https://github.com/zoni/obsidian-export/issues/371) for the block note support problem

2025-08-13 Wk 33 Wed - 15:43

Confirmed in gitlab that numbers are appended together also

2025-08-17 Wk 33 Sun - 12:38

Spawn [[#6.3 Investigate 370 numbered headings issue]] ^spawn-invst-cd3d77

### 2.4.1 Watch0

## 2.5 Find the rules that generate the vscode and github heading links

- [x] 

From [[#^spawn-task-b58ab0]] in [[#6.3 Investigate 370 numbered headings issue]]

2025-08-17 Wk 33 Sun

From [stackoverflow](https://stackoverflow.com/questions/51221730/markdown-link-to-header) - 22:52 -> [gitlab markdown docs (old)](https://solscm.tomatosystem.co.kr/help/user/markdown.md#header-ids-and-links),

```
The IDs are generated from the content of the header according to the following rules:

1. All text is converted to lowercase.
2. All non-word text (such as punctuation or HTML) is removed.
3. All spaces are converted to hyphens.
4. Two or more hyphens in a row are converted to one.
5. If a header with the same ID has already been generated, a unique incrementing number is appended, starting at 1.
```

This is not right, it gives `this-header-has-3-5-in-it-and-parentheses` for ` This header has 3.5 in it (and parentheses)` but numbers are close together...

2025-08-17 Wk 33 Sun - 22:49

Gitlab seems to have had a contradictory rule, and It doesn't seem specified in the standard? [CommonMark: Links](https://spec.commonmark.org/0.31.2/#links).

We will need to ensure to keep it behind an option.

Here we see they're called [link fragments](https://github.com/DavidAnson/markdownlint/blob/main/doc/Rules.md#md051---link-fragments-should-be-valid). 

2025-08-19 Wk 34 Tue - 08:49

Actually it seems we've been looking at old gitlab docs. They have an issue [#440733](https://gitlab.com/gitlab-org/gitlab/-/issues/440733) marking this design change to be consistent with other markdown parsers.

They want to be consistent with [Github Flavored Markdown (GFM)](https://github.github.com/gfm/) 

From [current gitlab heading ids and links](https://docs.gitlab.com/user/markdown/#heading-ids-and-links), 

In it 

```
## This heading has 3.5 in it (and parentheses)
```

yields

```
this-heading-has-35-in-it-and-parentheses
```

The rules are:

```
The IDs are generated from the content of the heading according to the following rules:

1. All text is converted to lowercase.
2. All non-word text (such as punctuation or HTML) is removed.
3. All spaces are converted to hyphens.
4. Two or more hyphens in a row are converted to one.
5. If a heading with the same ID has already been generated, a unique incrementing number is appended, starting at 1.
```

2025-08-19 Wk 34 Tue - 09:02

merge request [#92](https://gitlab.com/gitlab-org/ruby/gems/gitlab_kramdown/-/merge_requests/92) also explains the initial proposal on the change they made.

2025-08-19 Wk 34 Tue - 09:24

This [markdown guide](https://www.markdownguide.org/extended-syntax/) explores different markdown parsers and added features

This [wiki](https://deepwiki.com/zerodevx/zero-md/4.4-github-flavored-features#how-heading-ids-work) mentions implementation details of GFM on heading IDs, like in [marked-gfm-heading-id](https://github.com/markedjs/marked-gfm-heading-id).

The actual implementation is in [marked-gfm-heading-id/src/index.js](https://github.com/markedjs/marked-gfm-heading-id/blob/main/src/index.js). They do actually use a slugger, [github-slugger](https://github.com/Flet/github-slugger). 

This slugger is stateful. By keeping track of the heading being slugged, it appends `-N` to the end of a heading already encountered based on an internally tracked counter. Then it would be compatible with how github generates heading IDs for non-unique heading names.

2025-08-19 Wk 34 Tue - 09:47

Part of why I was looking for an implementation is that in the stated rules above

> 2. All non-word text (such as punctuation or HTML) is removed.

Is the least clear what this means. It seems a slugger like [github-slugger](https://github.com/Flet/github-slugger) should largely be able to handle this use case. 

It's unclear where it breaks consistency with [`slugify`](https://docs.rs/slugify/latest/slugify/), but we know one case for this is in `1.2.3` -> `1-2-3` vs `123`. 

Testing with vscode,

```
[](#a-b-c-d)
[](#1-2-3-4)
[](#1234)
[](#abcd)

# A-B-C-D

# 1-2-3-4

# A.B.C.D

# 1.2.3.4
```

Consistently, dots are stripped, so they are non-word text. Is there a different slugger for Rust that does this? 

There actually is! [crate.io github-slugger](https://crates.io/crates/github-slugger).

The actual change might just be to swap sluggers in our case.

Spawn [[#3.6 Test rust github-slugger for github heading dot omission compliance]] ^spawn-task-f2f61e

## 2.6 Test rust github-slugger for github heading dot omission compliance

- [x] 

From [[#^spawn-task-f2f61e]] in [[#3.5 Find the rules that generate the vscode and github heading links]]

2025-08-19 Wk 34 Tue - 09:57


(update)
In this, we want to compare slugifying results from [`slugify`](https://docs.rs/slugify/latest/slugify/) and [crate.io github-slugger](https://crates.io/crates/github-slugger).

2025-08-19 Wk 34 Tue - 13:42

They're actually using [slug](https://crates.io/crates/slug) and not [`slugify`](https://docs.rs/slugify/latest/slugify/). But in this instance they give the same results. 

Updated [rs_repro repro006](https://github.com/LanHikari22/rs_repro/blob/main/src/repro_tracked/repro006_slug_and_github_slugger.rs) and [#obsidian-export 370](https://github.com/zoni/obsidian-export/issues/370) accordingly

(/update)

2025-08-19 Wk 34 Tue - 10:50

2025-08-19 Wk 34 Tue - 10:59

Running [rs_repro repro006](https://github.com/LanHikari22/rs_repro/blob/main/src/repro_tracked/repro006_slug_and_github_slugger.rs),

| input          | slug         | github_slugger |
| -------------- | ------------ | -------------- |
| A.B.C.D        | a-b-c-d      | abcd           |
| 1.2.3.4        | 1-2-3-4      | 1234           |
| this-or-that   | this-or-that | this-or-that   |
| this--or-that  | this-or-that | this--or-that  |
| # Some heading | some-heading | -some-heading  |
| repeat         | repeat       | repeat         |
| repeat         | repeat       | repeat-1       |
| repeat         | repeat       | repeat-2       |

This can be run with

```sh
git clone https://github.com/LanHikari22/rs_repro.git && cd rs_repro && cargo run --features "repro006"
```

## 2.7 Open a PR to obsidian-export to close 370

- [ ] 

From [[#^spawn-task-612ee7]] in [[#6.3 Investigate 370 numbered headings issue]]

2025-08-19 Wk 34 Tue - 14:51

Currently we have a functional commit passing all pre-commit hook checks in `~/src/cloned/gh/zoni/branches/obsidian-export@fix-370-numbered-headings`. 

We simply replace the slugging dependency here.

We need to fork the project, and patch it with that commit, and review [CONTRIBUTING.md](https://github.com/zoni/obsidian-export/blob/main/CONTRIBUTING.md) again. Particularly, [nick's notes on high-quality-commits](https://nick.groenen.me/notes/high-quality-commits/).

2025-08-19 Wk 34 Tue - 14:55

```sh
git clone git@github.com:LanHikari22/obsidian-export.git ~/src/cloned/gh/LanHikari22/forked/zoni/obsidian-export
cd ~/src/cloned/gh/LanHikari22/forked/zoni/obsidian-export
git checkout -b fix-370-github-header-id-compliance
```

Spawn [[#5.3 Patch a new repository with a commit in another]]  ^spawn-howto-12f4da

2025-08-19 Wk 34 Tue - 15:24

```sh
# in /home/lan/src/cloned/gh/zoni/branches/obsidian-export@fix-370-numbered-headings
git format-patch 5fc119c^

# out
0001-replace-slug-with-github_slugger-for-compliance-with.patch
```

```sh
# in /home/lan/src/cloned/gh/LanHikari22/forked/zoni/obsidian-export
git am -3 ~/src/cloned/gh/zoni/branches/obsidian-export@fix-370-numbered-headings/0001-replace-slug-with-github_slugger-for-compliance-with.patch
```

I added `Cargo.lock` to `.gitignore` since It's autogenerated and not necessary to be tracked.

Setup and run [[#^obsidian-export-pre-commit-checks]],

```sh
pre-commit install

# out
pre-commit installed at .git/hooks/pre-commit
```

```sh
cargo +nightly fmt --
cargo test --all-targets --all-features
cargo clippy --all-targets --all-features -- -D warnings

# out (relevant)
[All OK]
```

2025-08-19 Wk 34 Tue - 15:46

Opened [PR #373](https://github.com/zoni/obsidian-export/pull/373).

2025-08-19 Wk 34 Tue - 15:56

We need to add a changelog also...

[CONTRIBUTING release notes](https://github.com/zoni/obsidian-export/blob/main/CONTRIBUTING.md#release-notes) states that it uses [just](https://github.com/casey/just#installation) and [Towncrier](https://towncrier.readthedocs.io/en/stable/index.html).

We also need `uvx`, provided by [uv](https://github.com/astral-sh/uv). This was not explained in the release notes, so I willl add that there.

```sh
sudo apt-get install just
python3 -m pip install uv
```

```sh
just add-changelog
```

### 2.7.1 Watch0

## 2.8 Test block identifier postprocessor impl

- [ ] 

From [[#^spawn-task-c79997]] in [[#6.4 Investigate fixing 371 block note links]]

2025-08-20 Wk 34 Wed - 16:43

```sh
cargo install --path .
```

Similar to [[#^trace-obsidian-export-events]],

```sh
export base_location="/home/lan/src/cloned/gh/LanHikari22"
export repo_name="lan-setup-notes"

obsidian-export "$base_location/$repo_name" "$base_location/branches/$repo_name@webview"

# out (relevant)
[nothing]
```

No changes were made to this document with respect to block identifiers.

2025-08-20 Wk 34 Wed - 17:16

Let's trace

```rust
Event::Text(cow_str) => {
	if !mut_in_code_block {
		if let Some((preceding_text, block_identifier)) =
			parse_preceding_text_and_block_identifier(cow_str)
		{
			println!("preceding_text: {preceding_text:?}, block_identifier: {block_identifier:?}");
[...]

// out
[nothing]
```

2025-08-20 Wk 34 Wed - 17:23

```rust
fn parse_preceding_text_and_block_identifier(line: &str) -> Option<(String, String)> {
    let reversed_line: String = line.chars().rev().collect();

    let rev_caret_position = reversed_line.find('^')?;
    let caret_position = line.len().checked_sub(1)?.checked_sub(rev_caret_position)?;

    println!("---");
    println!("line: {line}");
    println!("caret_position: {caret_position}");

    let (preceding_text, block_identifier) = {
        if caret_position == 0 {
            ("", line)
        } else {
            line.split_at_checked(caret_position)?
        }
    };

    println!("preceding_text: {preceding_text}, block_identifier: {block_identifier}");

    for c in block_identifier.chars() {
        if !(c.is_alphanumeric() || c == '-') {
            return None;
        }
    }

    println!("OK preceding_text: {preceding_text}, block_identifier: {block_identifier}");

    Some((preceding_text.to_owned(), block_identifier.to_owned()))
}
```

This yields many down to the last check, but no OKs.

```
---
line: ^spawn-invst-aa01cc
caret_position: 0
preceding_text: , block_identifier: ^spawn-invst-aa01cc
---
line: ^obsidian-export-pre-commit-checks
caret_position: 0
preceding_text: , block_identifier: ^obsidian-export-pre-commit-checks
---
line: ^trace-obsidian-export-events
caret_position: 0
preceding_text: , block_identifier: ^trace-obsidian-export-events
---
```

Some do have preceding text

```
---
line: But I do not have this installed. ^doc-issue1
caret_position: 34
preceding_text: But I do not have this installed. , block_identifier: ^doc-issue1
```

I think we're failing because of the caret itself.

It cannot pass this check:

```rust
for c in block_identifier.chars() {
	if !(c.is_alphanumeric() || c == '-') {
		return None;
	}
}
```

2025-08-20 Wk 34 Wed - 17:30

```rust
fn parse_preceding_text_and_block_identifier(line: &str) -> Option<(String, String)> {
    let reversed_line: String = line.chars().rev().collect();

    let rev_caret_position = reversed_line.find('^')?;
    let caret_position = line.len().checked_sub(1)?.checked_sub(rev_caret_position)?;

    let (preceding_text, block_identifier) = {
        if caret_position == 0 {
            ("", line)
        } else {
            line.split_at_checked(caret_position)?
        }
    };

    let block_identifier_no_caret: String = {
        block_identifier
            .chars()
            .skip(1)
            .collect()
    };

    println!("---");
    println!("line: {line}");
    println!("caret_position: {caret_position}");
    println!("preceding_text: {preceding_text}, block_identifier: {block_identifier_no_caret}");

    for c in block_identifier_no_caret.chars() {
        if !(c.is_alphanumeric() || c == '-') {
            return None;
        }
    }

    println!("OK preceding_text: {preceding_text}, block_identifier: {block_identifier_no_caret}");

    Some((preceding_text.to_owned(), block_identifier_no_caret.to_owned()))
}
```

```
line:  is very outdated (2016). Should Visidata recommend this? ^log-issue1
caret_position: 58
preceding_text:  is very outdated (2016). Should Visidata recommend this? , block_identifier: log-issue1
OK preceding_text:  is very outdated (2016). Should Visidata recommend this? , block_identifier: log-issue1
```

2025-08-20 Wk 34 Wed - 17:35

```diff
-Spawn [[#3.4 File issue about internal link behavior]] ^spawn-task-0ca517
+Spawn [3.4 File issue about internal link behavior](004%20Fix%20obsidian%20export%20to%20support%20internal%20links.md#34-file-issue-about-internal-link-behavior)<a name="spawn-task-0ca517" />
```

We do see `a` tags being generated now, but we need to add a space from preceding text first, and also we forgot to include the spawn itself again so it's seen.

- [ ] 2025-08-20 Wk 34 Wed - 17:39

```diff
-if preceding_text.is_empty() {
+if !preceding_text.is_empty() {
	mut_new_markdown_events.push(Event::Text(preceding_text.into()));
}
```

When I corrected that, the space issue with preceding text disappeared.

```diff
-Spawn [[#3.4 File issue about internal link behavior]] ^spawn-task-0ca517
+Spawn [3.4 File issue about internal link behavior](004%20Fix%20obsidian%20export%20to%20support%20internal%20links.md#34-file-issue-about-internal-link-behavior) <a name="spawn-task-0ca517" />
```

2025-08-20 Wk 34 Wed - 17:53

Added the block identifier as well:

```rust
mut_new_markdown_events.push(Event::Text(format!("^{block_identifier}").into()))
```

```diff
-Spawn [[#3.4 File issue about internal link behavior]] ^spawn-task-0ca517
+Spawn [3.4 File issue about internal link behavior](004%20Fix%20obsidian%20export%20to%20support%20internal%20links.md#34-file-issue-about-internal-link-behavior) <a name="spawn-task-0ca517" />^spawn-task-0ca517
```

This works!

Let's test it with the webview branch for this note repository!

2025-08-20 Wk 34 Wed - 19:19

Most of them work! But I noticed this one doesn't in a tutorial in delta-trace:

```diff
-Spawn [[#3.2 Setup a fresh installation of Windows on a VM]] ^spawn-task-603822
+Spawn [3.2 Setup a fresh installation of Windows on a VM](001%20Note%20Entry%20Header%20Meanings.md#32-setup-a-fresh-installation-of-windows-on-a-vm) ^spawn-task-603822
```

```diff
-from [[#^spawn-task-603822]] in [[#6.1 Investigate the Windows Operating System Internals]]
+from [<a name="spawn-task-603822" />^spawn-task-603822](001%20Note%20Entry%20Header%20Meanings.md#spawn-task-603822) in [6.1 Investigate the Windows Operating System Internals](001%20Note%20Entry%20Header%20Meanings.md#61-investigate-the-windows-operating-system-internals)
```

Those are both wrong. `a` tags should never be added in the middle of a link! And because that link came first, it likely skipped the actual block identifier, since we check to only add a tag per identifier once.

It also happens in

```
We add a code for the reference based on the heading code of this record (6.1), and a simple counter. So now we can refer to [duckduckgo](https://duckduckgo.com/) [<a name="6-1-1" />^6-1-1](001%20Note%20Entry%20Header%20Meanings.md#6-1-1)! The first link is the external link, the second is internal to where it is in the references for indexing purposes.
```

Another a tag somehow inside a link. I thought the number was not properly slugged, but actually in this case it's explicitly written as `6-1-1`. Slugging would make it `611` if it were `6.1.1` for example.

2025-08-20 Wk 34 Wed - 19:32

It should not have touched a link. So is this being processed as a Text? Some assumption we made is broken.

### 2.8.1 Pend
# 3 Issues

## 3.1 Linter errors on OK build for block identifier postprocessing

- [x] 

From [[#^spawn-issue-c72b4f]] in [[#6.4 Investigate fixing 371 block note links]]

2025-08-20 Wk 34 Wed - 15:09

From [[#^obsidian-export-pre-commit-checks]],

```sh
cargo +nightly fmt --
cargo test --all-targets --all-features
cargo clippy --all-targets --all-features -- -D warnings
```

When running

```sh
cargo clippy --all-targets --all-features -- -D warnings
```

2025-08-20 Wk 34 Wed - 15:12

```sh
error: arithmetic operation that can potentially result in unexpected side-effects
  --> src/postprocessors/block_identifier_links.rs:19:35
   |                                         
19 |         if reversed_line.len() <= caret_position + 1 {
   |                                   ^^^^^^^^^^^^^^^^^^
   |                 
   = help: for further information visit https://rust-lang.github.io/rust-clippy/master/index.html#arithmetic_side_effects
   = note: `-D clippy::arithmetic-side-effects` implied by `-D warnings`
   = help: to override `-D warnings` add `#[allow(clippy::arithmetic_side_effects)]`                           
```

Ooh, because arithmetic operations could implicitly panic, this makes the behavior explicit to avoid implicit crashes!

I have no reason to expect this to fail though.

```rust
if reversed_line.len()
	<= caret_position
		.checked_add(1)?
```

2025-08-20 Wk 34 Wed - 16:12

Instead of failing here, even though it's unexpected, it's better to just have the function itself enter fail mode. This is not a critical feature and so it should not crash the application.


2025-08-20 Wk 34 Wed - 15:18

```sh
    Checking obsidian-export v25.3.0 (/home/lan/src/cloned/gh/zoni/branches/obsidian-export@fix-371-block-note-links)
error: `to_string()` called on a `&str`            
  --> src/postprocessors/block_identifier_links.rs:24:13
   |                                         
24 |             "".to_string()                     
   |             ^^^^^^^^^^^^^^ help: try: `"".to_owned()`
   |                 
   = help: for further information visit https://rust-lang.github.io/rust-clippy/master/index.html#str_to_string
   = note: `-D clippy::str-to-string` implied by `-D warnings`
   = help: to override `-D warnings` add `#[allow(clippy::str_to_string)]`
```

`String` is an owned variant of `str`, which is more direct than `.to_string()` which would just do this. But we could also here do `String::new()` directly, which is what the linter suggests next.

2025-08-20 Wk 34 Wed - 15:34

```sh
error: indexing into a string may panic if the index is within a UTF-8 character
  --> src/postprocessors/block_identifier_links.rs:26:13
   |                                         
26 |             reversed_line[caret_position + 1..].chars().rev().collect()
   |             ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
   |                 
   = help: for further information visit https://rust-lang.github.io/rust-clippy/master/index.html#string_slice
   = note: `-D clippy::string-slice` implied by `-D warnings`
   = help: to override `-D warnings` add `#[allow(clippy::string_slice)]`                                      
```

Hmm, this should be a valid position, but let's do this another way:

```rust
let (preceding_text, block_identifier) = {
	if caret_position == 0 {
		("", line)
	} else {
		line.split_at_checked(caret_position)?
	}
};
```

2025-08-20 Wk 34 Wed - 16:30

```sh
error: this `match` can be collapsed into the outer `match`
  --> src/postprocessors/block_identifier_links.rs:71:34
   |                                                                                           
71 |               Event::Start(tag) => match tag {
   |  __________________________________^                                                      
72 | |                 Tag::CodeBlock(_) => {                                                  
73 | |                     mut_in_code_block = true;
...  |               
76 | |             },
   | |_____________^
   |                                                                                                                                                                                          
help: the outer pattern can be modified to include the inner pattern
  --> src/postprocessors/block_identifier_links.rs:71:26
   |                                                                                           
71 |             Event::Start(tag) => match tag {
   |                          ^^^ replace this binding
72 |                 Tag::CodeBlock(_) => {
   |                 ^^^^^^^^^^^^^^^^^ with this pattern
   = help: for further information visit https://rust-lang.github.io/rust-clippy/master/index.html#collapsible_match
   = note: `-D clippy::collapsible-match` implied by `-D warnings`
   = help: to override `-D warnings` add `#[allow(clippy::collapsible_match)]`
```

```rust
Event::Start(Tag::CodeBlock(_)) => {
	mut_in_code_block = true;
}
```

2025-08-20 Wk 34 Wed - 16:38

```
error: comparison to empty slice
  --> src/postprocessors/block_identifier_links.rs:98:32
   |
98 | ...                   if preceding_text != "" {
   |                          ^^^^^^^^^^^^^^^^^^^^ help: using `!is_empty` is clearer and more explicit: `!preceding_text.is_empty()`
   |
   = help: for further information visit https://rust-lang.github.io/rust-clippy/master/index.html#comparison_to_empty
   = note: `-D clippy::comparison-to-empty` implied by `-D warnings`
   = help: to override `-D warnings` add `#[allow(clippy::comparison_to_empty)]`

error: could not compile `obsidian-export` (lib) due to 1 previous error
warning: build failed, waiting for other jobs to finish...
error: could not compile `obsidian-export` (lib test) due to 1 previous error
```

(update)
```rust
if !preceding_text.is_empty() {
	mut_new_markdown_events.push(Event::Text(preceding_text.into()));
}
```

<details>
<summary>errata</summary>

2025-08-20 Wk 34 Wed - 17:44

Corrected logic, was a flipped conditional when I changed it.

</details>
(/update)


2025-08-20 Wk 34 Wed - 16:40

```sh
cargo +nightly fmt --
cargo test --all-targets --all-features
cargo clippy --all-targets --all-features -- -D warnings

# out (relevant)
[all OK]
```

# 4 HowTos

## 4.1 Revert git history back to fresh clone state for main branch

- [x] 

2025-08-13 Wk 33 Wed - 10:34

Basically, I want to be able to go back to the commit on origin main and have that be the latest content I have.

My use case is that I am running into merge conflicts with myself due to force pushing, and all of this is unnecessary, if I can just revert back the  git history back to a fresh clone state.

We can avoid those merge conflicts with 

```
git reset --hard origin/main
```

Similar to suggestion from this [stackoverflow answer](https://stackoverflow.com/a/52995940/6944447).

## 4.2 Trace class in heading html definition

- [x] Use case works for the div, but for the h3 it seems to have a missing css selector class

From [[#^spawn-howto-e804cf]] in [[#3.2 Attempting to reproduce header jump behavior in local html]]

2025-08-13 Wk 33 Wed - 12:46

For example in [checkpipe README case 1](https://github.com/LanHikari22/checkpipe/blob/4373f01a6c17cd524b13723373f6de7897f2520e/README.md#case-1-basic-mapping-and-filtering),

If we inspect "Case 1: Basic mapping and filtering" we get

```html
<h3 tabindex="-1" class="heading-element" dir="auto" style="background-color: transparent;">Case 1: Basic mapping and filtering</h3>
```

I wanna trace that class `heading-element`.

2025-08-13 Wk 33 Wed - 12:56

This is the [HTML Standard](https://html.spec.whatwg.org/multipage/) ([/dev/ version](https://html.spec.whatwg.org/dev/)).

From [/dev/ attributes](https://html.spec.whatwg.org/dev/indices.html#attributes-3) -> class -> [classes](https://html.spec.whatwg.org/dev/dom.html#classes),

> Assigning classes to an element affects class matching in selectors in CSS, the [getElementsByClassName()](https://dom.spec.whatwg.org/#dom-document-getelementsbyclassname) method in the DOM, and other such features.

This is the [CSS Snapshot 2024 Specification](https://www.w3.org/TR/css-2024/).

From a section on [selectors](https://www.w3.org/TR/selectors-3/#selector),

> A Selector represents a structure. This structure can be used as a condition (e.g. in a CSS rule) that determines which elements a selector matches in the document tree, or as a flat description of the HTML or XML fragment corresponding to that structure.

From this [stackoverflow answer](https://stackoverflow.com/a/29518399/6944447),

They suggest that it would typically be available under `Styles` in chrome during inspection, and you could jump to the specific css declaration for it. 

In Firefox they're called Rules instead, for CSS rules I think. 

We are able to find the CSS selector for the div above it, but not the h3 heading itself:

![[Pasted image 20250813132835.png]]

2025-08-13 Wk 33 Wed - 13:32

Nothing changes when we put another invalid class for the h3, so it might just not be configured?

## 4.3 Patch a new repository with a commit in another

- [x] 

From [[#^spawn-howto-12f4da]] in [[#3.7 Open a PR to obsidian-export to close 370]]

2025-08-19 Wk 34 Tue - 15:12

[stackoverflow answer](https://stackoverflow.com/a/3816292/6944447) -> {[git-format-patch](https://www.kernel.org/pub/software/scm/git/docs/git-format-patch.html), [git-am](https://www.kernel.org/pub/software/scm/git/docs/git-am.html)}

For my use case,

```sh
# in /home/lan/src/cloned/gh/zoni/branches/obsidian-export@fix-370-numbered-headings
git format-patch 5fc119c^

# out
0001-replace-slug-with-github_slugger-for-compliance-with.patch
```

```sh
# in /home/lan/src/cloned/gh/LanHikari22/forked/zoni/obsidian-export
git am -3 ~/src/cloned/gh/zoni/branches/obsidian-export@fix-370-numbered-headings/0001-replace-slug-with-github_slugger-for-compliance-with.patch
```


# 5 Investigations

## 5.1 What does the markdown standard say about internal page links?

- [x] 

From [[#^spawn-invst-d8b7ce]] in [[#3.1 Capture details on the broken links problem]]

2025-08-13 Wk 33 Wed - 11:10

Here is the [v0.31.2 CommonMark Spec](https://spec.commonmark.org/0.31.2/).

Spawn [[#8.1 Commentary on things learned from CommonMark standard]] ^spawn-side-58a24b

2025-08-13 Wk 33 Wed - 11:40

Their [example 501](https://spec.commonmark.org/0.31.2/#example-501) seems to be relevant. They discuss fragment identifiers like `#fragment` in this.

```markdown
[link](#fragment)
```

It apparently translates to

```html
<p><a href="#fragment">link</a></p>
```

This link will work on a `# Fragment` heading in obsidian:

![[Pasted image 20250813114250.png]]

The html one will not work in obsidian:

![[Pasted image 20250813114610.png]]

So we need to learn about these fragment identifiers. They are not mentioned anywhere else in this specification.  I'm guessing they are handled directly by `href` there.

## 5.2 Why is github markdown rendering adding full repo information for some internal markdown links but not others?

- [ ] 

From [[#^spawn-invst-767a80]] in [[#3.1 Capture details on the broken links problem]]

### 5.2.1 Low Prio
## 5.3 Investigate 370 numbered headings issue

- [x] 

From [[#^spawn-invst-cd3d77]] in [[#3.4 File issue about internal link behavior]]

2025-08-17 Wk 33 Sun - 12:39

Let's clone the repository and investigate the issue. If we resolve it, we can fork and open a PR.

```sh
git clone git@github.com:zoni/obsidian-export.git ~/src/cloned/gh/zoni/branches/obsidian-export@fix-370-numbered-headings
cd ~/src/cloned/gh/zoni/branches/obsidian-export@fix-370-numbered-headings
git checkout -b fix-370-numbered-headings
```

```sh
cargo build

# out (relevant)
info: downloading component 'rustc'
error: component download failed for rustc-x86_64-unknown-linux-gnu: could not rename downloaded file from '/home/lan/.rustup/downloads/e8395c5c5756253b76107055e093ffbc4431af7b30aeebe72ce2684b9cb53973.partial' to '/home/lan/.rustup/downloads/e8395c5c5756253b76107055e093ffbc4431af7b30aeebe72ce2684b9cb53973': No such file or directory (os error 2)
```

But if you run it again, it builds fine. Weird.

Check out [CONTRIBUTING.md](https://github.com/zoni/obsidian-export/blob/main/CONTRIBUTING.md).

```sh
rustup toolchain install nightly --component rustfmt
```

Spawn [[#7.1 Setting up rustfmt and clippy in CI]] ^spawn-idea-b9d8a7

2025-08-17 Wk 33 Sun - 13:02

Need to set up pre-commit

```sh
cat .pre-commit-config.yaml | grep entry:

# out
entry: cargo +nightly fmt --
entry: cargo test --all-targets --all-features
entry: cargo clippy --all-targets --all-features -- -D warnings
```

```sh
python3 -m pip install pre-commit
```

```sh
pre-commit install

# out
pre-commit installed at .git/hooks/pre-commit
```

We need to ensure to write integration tests and possibly also unit tests for our change here.

2025-08-17 Wk 33 Sun - 13:33

Let's make sure the pre-commit hooks all work

```sh
cargo +nightly fmt --
cargo test --all-targets --all-features
cargo clippy --all-targets --all-features -- -D warnings
```

^obsidian-export-pre-commit-checks

2025-08-17 Wk 33 Sun

`lib.rs > Exporter::make_link_to_file`  uses  [`slugify`](https://docs.rs/slugify/latest/slugify/) which has the documented usage

```rust
assert_eq!(slugify("My Test String!!!1!1"), "my-test-string-1-1");
```

This is not enough. For it to be correctly interpreted as a markdown link it needs to be interpreted as `#my-test-string11`, so we should not be using slugify for this.

Spawn [[#3.5 Find the rules that generate the vscode and github heading links]] ^spawn-task-b58ab0

2025-08-19 Wk 34 Tue - 11:48

We wrote a report on our findings in [obsidian-export #370](https://github.com/zoni/obsidian-export/issues/370).

2025-08-19 Wk 34 Tue - 13:54

So in preperation of a PR, I replaced slug with github_slugger.

But right now I'm still failing a test I wrote

```
test_github_flavored_markdown_heading_id_compliance

<[Some heading](Note.md#-some-heading)
>[Some heading](Note.md#some-heading)
```

Tracing near [this line](https://github.com/zoni/obsidian-export/blob/49336d69fc117caf6f623fcab2af01e494949351/src/lib.rs#L781),

```rust
println!("section: {:?}", reference.section.unwrap());
println!("slug: {:?}", slug(reference.section.unwrap()));

if let Some(section) = reference.section {
	link.push('#');
	link.push_str(&slug(section));
}

println!("link: {link:?}");

// out (relevant)
section: "Some heading"
slug: "some-heading"
link: "Note.md#some-heading"
```

This seems to be because of obsidian. `[[#Some heading]]` is ambiguous with respect to `Some heading` or `\# Some heading`, so I'm not gonna include this test case.

2025-08-19 Wk 34 Tue - 14:13

All tests are passing, pre-commit hooks checks are all passing.

Let me try to test this with my repostitory here's `webview` branch and see if it fixes many of my internal links!

```sh
cargo install --path .
```

It works!

2025-08-19 Wk 34 Tue - 14:51

This is PR ready.

Spawn [[#3.7 Open a PR to obsidian-export to close 370]] ^spawn-task-612ee7

## 5.4 Investigate fixing 371 block note links

- [ ] 

2025-08-20 Wk 34 Wed - 09:38

I added a [comment](https://github.com/zoni/obsidian-export/issues/371#issuecomment-3204375164) in [obsidian-export #371](https://github.com/zoni/obsidian-export/issues/371) explaining the strategy. 

We just have to add `a` html tags to make the block note anchors work.

```sh
export branch="fix-371-block-note-links"
git clone git@github.com:zoni/obsidian-export.git ~/src/cloned/gh/zoni/branches/obsidian-export@$branch
cd ~/src/cloned/gh/zoni/branches/obsidian-export@$branch
git checkout -b $branch
```

2025-08-20 Wk 34 Wed - 09:53

This solution builds up on top of [obsidian-export PR #373](https://github.com/zoni/obsidian-export/pull/373). We need to patch with that content.

```sh
# in /home/lan/src/cloned/gh/LanHikari22/forked/zoni/obsidian-export
git format-patch bfc3eca^..

# out
0001-Use-github-slugger-in-place-of-slug.patch
0002-Update-CONTRIBUTING.md-with-uv-requirement.patch
0003-Add-changelog-fragment-for-github-header-link-fix.patch
```

We only functionally need `0001-Use-github-slugger-in-place-of-slug.patch`. 

```sh
# in /home/lan/src/cloned/gh/zoni/branches/obsidian-export@fix-371-block-note-links
git am -3 ~/src/cloned/gh/LanHikari22/forked/zoni/obsidian-export/0001-Use-github-slugger-in-place-of-slug.patch

# out
Applying: Use github-slugger in place of slug
```

Clean up the patch files,

```sh
# in /home/lan/src/cloned/gh/LanHikari22/forked/zoni/obsidian-export
git reset --hard
```

2025-08-20 Wk 34 Wed - 10:02

```sh
pre-commit install

# out
pre-commit installed at .git/hooks/pre-commit
```

2025-08-20 Wk 34 Wed - 10:13

This likely should be implemented as a postprocessor where every `^{name}` is replaced with `<a name={slug} />^{name}`

We can use [exporter.add_postprocessor](https://github.com/zoni/obsidian-export/blob/49336d69fc117caf6f623fcab2af01e494949351/src/lib.rs#L344). It takes functions of the type `impl Fn(&mut Context, &mut Vec<Event<'_>>) -> PostprocessorResult`.

2025-08-20 Wk 34 Wed - 10:43

In this [line](https://github.com/pulldown-cmark/pulldown-cmark/blob/f4a326d225e79412b5ecabd1c241c851e8160815/pulldown-cmark/src/lib.rs#L200) in dependency [pulldown-cmark](https://github.com/pulldown-cmark/pulldown-cmark/), it says:

>     /// The `BlockQuoteKind` is only parsed & populated with [`Options::ENABLE_GFM`], `None` otherwise.

That seems to be the only use for it I can find in there.

2025-08-20 Wk 34 Wed - 11:05

So we don't need to use `Context` for this, but we do need to find the appropriate markdown event, and replace it with a different event for our block notes. <a name="test-block-note-for-tracing" />^test-block-note-for-tracing

Spawn [[#6.5 Tracing events passed to postprocessor to impl block note postprocessor]] ^spawn-invst-aa01cc

2025-08-20 Wk 34 Wed - 12:19

![[#6.5.1 Block Identifier Filter Requirements]]

2025-08-20 Wk 34 Wed - 12:20

Moved `postprocessors` into its own module folder. This should be more maintainable than to squeeze every possible postprocessor into the same file. 

2025-08-20 Wk 34 Wed - 15:06

Spawn [[#4.1 Linter errors on OK build for block identifier postprocessing]] ^spawn-issue-c72b4f

2025-08-20 Wk 34 Wed - 16:41

We implemented the logic for this, now for testing.

Spawn [[#3.8 Test block identifier postprocessor impl]] ^spawn-task-c79997


### 5.4.1 Pend

## 5.5 Tracing events passed to postprocessor to impl block note postprocessor

- [x] 

From [[#^spawn-invst-aa01cc]] in [[#6.4 Investigate fixing 371 block note links]]

2025-08-20 Wk 34 Wed - 11:05

Let's trace!

```rust
pub fn include_a_tags_with_block_notes(
    _context: &mut Context,
    events: &mut MarkdownEvents<'_>,
) -> PostprocessorResult {
    for event in events.iter_mut() {
        println!("event: {event:?}");
    }
    PostprocessorResult::Continue
}
```

From [[#^obsidian-export-pre-commit-checks]],

```sh
# in /home/lan/src/cloned/gh/zoni/branches/obsidian-export@fix-371-block-note-links
cargo +nightly fmt --
cargo test --all-targets --all-features
cargo clippy --all-targets --all-features -- -D warnings

# out (relevant)
[all OK]
```

```sh
# in /home/lan/src/cloned/gh/zoni/branches/obsidian-export@fix-371-block-note-links
cargo install --path .
```

```sh
export base_location="/home/lan/src/cloned/gh/LanHikari22"
export repo_name="lan-setup-notes"

obsidian-export "$base_location/$repo_name" "$base_location/branches/$repo_name@webview" | grep "test-block-note-for-tracing"

# out (relevant)
event: Text(Boxed(" for this, but we do need to find the appropriate markdown event, and replace it with a different event for our block notes. ^test-block-note-for-tracing"))
```
^trace-obsidian-export-events

So we're looking for a `Text` event, and we need to replace it with another `Text` event. This should be done to the first one encountered, or our above tracing example would break things. But more correctly, nothing inside `backticks` or code blocks should ever be converted in this way, as this would break presentation.

We can avoid code blocks easily:

```
event: End(CodeBlock)
event: Start(CodeBlock(Fenced(Boxed("sh"))))
event: Text(Boxed("export base_location=\"/home/lan/src/cloned/gh/LanHikari22\"\nexport repo_name=\"lan-setup-notes\"\n\nobsidian-export \"$base_location/$repo_name\" \"$base_location/branches/$repo_name@webview\" | grep \"test-block-note-for-tracing\"\n\n# out (relevant)\nevent: Text(Boxed(\" for this, but we do need to find the appropriate markdown event, and replace it with a different event for our block notes. ^test-block-note-for-tracing\"))\n"))
event: End(CodeBlock)
```

The `backticks` also aren't text events themselves:

```
event: Code(Boxed("backticks"))
```

So, as long as a `CodeBlock` has not been started and it is in a `Text` event.

2025-08-20 Wk 34 Wed - 11:33

For `<a name="test-block-note-for-tracing" />^test-block-note-for-tracing`,

```
event: InlineHtml(Boxed("<a name=\"test-block-note-for-tracing\" />"))
event: Text(Boxed("^test-block-note-for-tracing"))
```

2025-08-20 Wk 34 Wed - 11:56

So we need to possibly consider to not include an `InlineHtml` if one is already included. We also need to consider splitting `Text` events into `Text` -> `InlineHtml` -> `Text`. Obsidian will not allow anything to come after a note block, so we are guaranteed that the last `Text` is just the caret note block declaration.

2025-08-20 Wk 34 Wed - 12:08

Okay the `^stuff` is called a Block Identifier according to [obsidian](https://help.obsidian.md/links#Link+to+a+block+in+a+note).

2025-08-20 Wk 34 Wed - 14:00

Quickly testing other types like html blocks, but so far only code blocks embed text within them that we should avoid. We don't have to bother with paragraph blocks.

### 5.5.1 Block Identifier Filter Requirements

1. `CodeBlock` must not be open.
2. Only considers `Text` events for presence of a note block.
3. Only considers the end of a `Text` event having an `^{name}` format. 
4. Cannot have a space after `^{name}`, it must be the last thing in the line.
5. Block identifiers processed must be compliant with [description in obsidian docs](https://help.obsidian.md/links#Link+to+a+block+in+a+notehttps://help.obsidian.md/links#Link+to+a+block+in+a+note): "Block identifiers can only consist of Latin letters, numbers, and dashes."
6. If an identifier block is defined multiple times within the same file, only include an a tag for the first.

# 6 Ideas

## 6.1 Setting up rustfmt and clippy in CI

From [[#^spawn-idea-b9d8a7]] in [[#6.3 Investigate 370 numbered headings issue]]

2025-08-17 Wk 33 Sun - 13:02

In [obsidian-export > CONTRIBUTING.md](https://github.com/zoni/obsidian-export/blob/main/CONTRIBUTING.md),

> In addition, [clippy](https://github.com/rust-lang/rust-clippy) is configured to be quite pedantic and all of its checks must also pass for CI builds to succeed.

We could setup checks like this too for rust projects. clippy runs checks against the code base content, and rustfmt maintains consistent styling!

We can even runs hooks directly:

> This codebase is set up with the [pre-commit framework](https://pre-commit.com/) to automatically run the appropriate checks locally whenever you commit. Assuming you [have pre-commit installed](https://pre-commit.com/#install), all you need to do is run `pre-commit install` once to get this set up.

2025-08-17 Wk 33 Sun - 13:15

They also have cool github-friendly callout styles:

> **💡 Tip:** Some tip
> Content of the tip


> **⚠ Warning**
> Content of the warning!
> Explained at length!

2025-08-17 Wk 33 Sun - 13:20

There's many cool practices in that [CONTRIBUTING.md](https://github.com/zoni/obsidian-export/blob/main/CONTRIBUTING.md) document! 

auto-generated README, [towncrier](https://towncrier.readthedocs.io/en/stable/index.html) for autogenerated release notes from fragments...

They also have cool guides to [rust documentation](https://doc.rust-lang.org/rustdoc/how-to-write-documentation.html)  from rust themselves and [rust by example](https://doc.rust-lang.org/rust-by-example/meta/doc.html). 

In [rust documentation](https://doc.rust-lang.org/rustdoc/how-to-write-documentation.html), it mentions,

> It is recommended that each item's documentation follows this basic structure:

```text
[short sentence explaining what it is]

[more detailed explanation]

[at least one code example that users can copy/paste to try it]

[even more advanced explanations if necessary]
```

## 6.2 Contributing to markdown-it-plugins

2025-08-19 Wk 34 Tue - 10:11

In [README.md](https://github.com/markdown-it-rust/markdown-it-plugins.rs/blob/main/README.md), 

[gfm-autolinks](https://github.com/markdown-it-rust/markdown-it-plugins.rs/blob/main/crates/gfm_autolinks/README.md) link is broken. It should be [this](https://github.com/markdown-it-rust/markdown-it-plugins.rs/blob/main/crates/gfm-autolinks/README.md). Issued [#24](https://github.com/markdown-it-rust/markdown-it-plugins.rs/issues/24).


The project has no license. Issued [#25](https://github.com/markdown-it-rust/markdown-it-plugins.rs/issues/25).

## 6.3 Including pre-commit hooks in my repositories

2025-08-19 Wk 34 Tue - 16:29

Consistent styling, additional linting beyond errors, ensuring that all tests pass, always on commit, seems like very good practice. 

[obsidian-export](https://github.com/zoni/obsidian-export) is a very good example of how this was setup for a Rust project. It's in general a good example with documentation as well.

# 7 Side Notes

## 7.1 Commentary on things learned from CommonMark standard


From [[#^spawn-side-58a24b]] in [[#6.1 What does the markdown standard say about internal page links?]]

### 7.1.1 About Links

[CommonMark: Links](https://spec.commonmark.org/0.31.2/#links).

I didn't know you could do links this way! This may be way better for how I do references!

Similar to [example 527](https://spec.commonmark.org/0.31.2/#example-527),

[boog][goog]

[goog]: https://www.google.com/

### 7.1.2 About AutoLinks

[CommonMark: AutoLinks](https://spec.commonmark.org/0.31.2/#autolinks)

Those are interesting, I didn't know you could have `mailto` with this like <mailto:user@gmail.com>. Obsidian even recognizes i different!

![[Pasted image 20250813111924.png]]
And it opens directly in the default mail application.

## 7.2 Interesting things in obsidian-export README and repo

2025-08-13 Wk 33 Wed - 15:05

They mention a workaround for hugo. This [article](https://www.makeuseof.com/hugo-static-site-generator/) mentiones it's a static website generator that uses markdown. Its github link is [here](https://github.com/gohugoio/hugo).

2025-08-13 Wk 33 Wed - 15:15

They use [renovate](https://github.com/apps/renovate). Which seems to be a bot that opens many PRs for tooling updates.

2025-08-20 Wk 34 Wed - 10:09

![[Pasted image 20250820100939.png]]

I didn't know I could have intellisense-backed references like this in code documentation!

## 7.3 Interesting programmatic spec generation in gitlab source

2025-08-19 Wk 34 Tue - 09:10

[header_anchors_spec.rb](https://gitlab.com/gitlab-org/ruby/gems/gitlab-glfm-markdown/-/blob/3c81857167ce163ea860d5b2703f3d149b12fd22/spec/header_anchors_spec.rb)

This seems like it generates specification documents? Or is it running tests? There's a lot of expects in there.

2025-08-19 Wk 34 Tue - 09:34

Similar thing is happening in [marked-gfm-heading-id spec](https://github.com/markedjs/marked-gfm-heading-id/blob/main/spec/index.test.js).

## 7.4 Obsidian-export strange behavior

2025-08-20 Wk 34 Wed - 11:41

Tracing using the method in [[#^trace-obsidian-export-events]],

```
event: Text(Boxed("/// The `BlockQuoteKind` is only parsed & populated with [`Options::ENABLE_GFM`], `None` otherwise.\n"))
event: End(CodeBlock)
event: Text(Boxed("602.6"))
event: End(TableCell)
event: Start(TableCell)
event: Text(Boxed("587.2"))
event: End(BlockQuote(None))

event: Start(TableCell)
event: Text(Boxed("615.4"))
event: End(TableCell)
```

This text doesn't appear anywhere else. Why TableCell? There was no tables around these parts!

# 8 External Links

| Link                                                                                                                                                                                                                                                 | In                                                                                                                           |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| [link](https://github.com/LanHikari22/lan-setup-notes/blob/webview/lan/topics/tooling/obsidian/tasks/2025/004%20Fix%20obsidian%20export%20to%20support%20internal%20links.md#34-file-issue-about-internal-link-behavior)                             | [obsidian-export #370](https://github.com/zoni/obsidian-export/issues/370)                                                   |
| [link](https://github.com/LanHikari22/lan-setup-notes/blob/webview/lan/topics/tooling/obsidian/tasks/2025/004%20Fix%20obsidian%20export%20to%20support%20internal%20links.md#36-test-rust-github-slugger-for-github-heading-dot-omission-compliance) | [rs_repro repro006](https://github.com/LanHikari22/rs_repro/blob/main/src/repro_tracked/repro006_slug_and_github_slugger.rs) |
| [link](https://github.com/LanHikari22/lan-setup-notes/blob/webview/lan/topics/tooling/obsidian/tasks/2025/004%20Fix%20obsidian%20export%20to%20support%20internal%20links.md#37-open-a-pr-to-obsidian-export-to-close-370)                           | [obsidian-export PR #373](https://github.com/zoni/obsidian-export/pull/373)                                                  |

# 9 References
