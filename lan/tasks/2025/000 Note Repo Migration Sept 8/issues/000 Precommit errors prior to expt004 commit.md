---
parent: '[[000 Note Repo Migration Sept 8]]'
spawned_by: '[[000 Note Repo Migration Sept 8]]'
context_type: issue
status: done
---

Parent: [000 Note Repo Migration Sept 8](../000%20Note%20Repo%20Migration%20Sept%208.md)

Spawned by: [000 Note Repo Migration Sept 8](../000%20Note%20Repo%20Migration%20Sept%208.md)

Spawned in: [<a name="spawn-issue-7a20bd" />^spawn-issue-7a20bd](../000%20Note%20Repo%20Migration%20Sept%208.md#spawn-issue-7a20bd)

# 1 Journal

2025-09-17 Wk 38 Wed - 03:52 +03:00

````sh
# in /home/lan/src/cloned/gh/deltachives/2025-Wk37-000-obsidian-migration
git add .
pre-commit run
````

2025-09-17 Wk 38 Wed - 03:57 +03:00

````rust
error: invalid character ' ' in crate name: `expt000_parse_single_pulldown_cmark copy`                                                                                                        
  |                                                                                            
  = help: you can either pass `--crate-name` on the command line or add `#![crate_name = "…"]` to set the crate name
                                               
error: could not compile `migration_rs` (bin "expt000_parse_single_pulldown_cmark copy" test) due to 1 previous error

````

Oops it had a space.

````rust
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
````

That's definitely simpler

2025-09-17 Wk 38 Wed - 03:59 +03:00

````rust
error: writing `&PathBuf` instead of `&Path` involves a new object where a slice will do
  --> src/cluster_note.rs:79:49
   |                                                                                                                                                                                          
79 | pub fn file_exists_in_folder_of_same_name(path: &PathBuf) -> bool {
   |                                                 ^^^^^^^^ help: change this to: `&Path`
   |                                                                                           
   = help: for further information visit https://rust-lang.github.io/rust-clippy/master/index.html#ptr_arg
   = note: `-D clippy::ptr-arg` implied by `-D warnings`
   = help: to override `-D warnings` add `#[allow(clippy::ptr_arg)]`
````

So `Path` is like a slice type for `PathBuf`! I guess with it being a slice instead, many different data structures could be used. Just like you don't use `&Vec<T>` when you can get away with `&[T]` because you could use slicing operations as well as arrays and vectors would be usable with that function.

2025-09-17 Wk 38 Wed - 04:07 +03:00

````rust
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
````

Okay. `bool::default()` does give `false`.

````rust
inner_fn().unwrap_or_default()
````

So we either get the result or the default!

2025-09-17 Wk 38 Wed - 04:21 +03:00

````rust
error: called `filter(..).next()` on an `Iterator`. This is more succinctly expressed by calling `.find(..)` instead
````

````rust
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
````

Really? `filter_map(|opt_path| opt_path)`?

It works! It somehow knows to just take out the `Nones` that way!

That's way better than what I did where I explained why I did `.expect`!

2025-09-17 Wk 38 Wed - 04:31 +03:00

````rust
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
````

Eeeh. I guess it is unnecessary, but I usually create my functions as a collection of immutable let bindings. But it makes sense that the name of the last one should simply be denoted by the function name. Doing it that way though allows for easy tracing. But we can always turn it into a let binding if we want to trace, so not a big deal.

It seems happy though with the pattern here:

````rust
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
````

since we do a little processing in the end. I do this because of the tracing point made earlier, and to document the final happy path value expression.

Still let's make it more concise as

````rust
pub fn note_link_to_path(vault: &[WorkingPath], note_link: &str) -> Option<PathBuf> {
    vault
        .iter()
        .map(|item| match item {
			// ...
        })
        .filter_map(|opt_path| opt_path)
        .next()
}
````

as it is consistent with the previous change and it's a bit redundant to capture it as an Option again.

2025-09-17 Wk 38 Wed - 04:43 +03:00

````rust
error: use of `filter_map` with an identity function
   --> src/cluster_note.rs:433:10
    |
433 |         .filter_map(|opt_path| opt_path)
    |          ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ help: try: `flatten()`
    |
    = help: for further information visit https://rust-lang.github.io/rust-clippy/master/index.html#filter_map_identity
    = note: `-D clippy::filter-map-identity` implied by `-D warnings`
    = help: to override `-D warnings` add `#[allow(clippy::filter_map_identity)]`
````

Could've suggested that first...

````rust
error: this expression creates a reference which is immediately dereferenced by the compiler
   --> src/cluster_note.rs:117:32
    |
117 |     if !is_cluster_root_folder(&folder.parent()?)? {
    |                                ^^^^^^^^^^^^^^^^^ help: change this to: `folder.parent()?`
    |
    = help: for further information visit https://rust-lang.github.io/rust-clippy/master/index.html#needless_borrow
    = note: `-D clippy::needless-borrow` implied by `-D warnings`
    = help: to override `-D warnings` add `#[allow(clippy::needless_borrow)]`
````

Oops, `.parent()` returns an `Option<&Path>` so with `?` that's already a `&Path`.

2025-09-17 Wk 38 Wed - 12:19 +03:00

````
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

````

2025-09-17 Wk 38 Wed - 12:22 +03:00

````
error: using `clone` on type `HeadingLevel` which implements the `Copy` trait
   --> src/common.rs:302:25
    |
302 |                         heading_level.clone(),
    |                         ^^^^^^^^^^^^^^^^^^^^^ help: try dereferencing it: `*heading_level`
    |
    = help: for further information visit https://rust-lang.github.io/rust-clippy/master/index.html#clone_on_copy
````

2025-09-17 Wk 38 Wed - 12:38 +03:00

Finally done
