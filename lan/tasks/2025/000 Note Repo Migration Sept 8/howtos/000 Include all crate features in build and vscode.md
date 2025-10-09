---
parent: '[[000 Note Repo Migration Sept 8]]'
spawned_by: '[[003 Make everything under lan_rs_common featured for very minimal includes]]'
context_type: howto
status: done
---

Parent: [000 Note Repo Migration Sept 8](../000%20Note%20Repo%20Migration%20Sept%208.md)

Spawned by: [003 Make everything under lan_rs_common featured for very minimal includes](../tasks/003%20Make%20everything%20under%20lan_rs_common%20featured%20for%20very%20minimal%20includes.md)

Spawned in: [<a name="spawn-howto-358a72" />^spawn-howto-358a72](../tasks/003%20Make%20everything%20under%20lan_rs_common%20featured%20for%20very%20minimal%20includes.md#spawn-howto-358a72)

# 1 Journal

2025-09-09 Wk 37 Tue - 19:38 +03:00

I have a vscode `.vscode/settings.json` file

````json
{
  "rust-analyzer.cargo.features": [
  ]
}
````

But what do I put in there to ensure all features are included?

Here are the [cargo book docs on features](https://doc.rust-lang.org/cargo/reference/features.html).

Over CLI, we can use

 > 
 > * `--all-features`: Activates all features of all packages selected on the command line.

Now what about vscode?

This issue [gh rust-lang/rust-analyzer #19630](https://github.com/rust-lang/rust-analyzer/issues/19630) mentions that it is possible to pass "all" to `"rust-analyzer.cargo.features"`

We could use `dep:crate_name` instead of all the `use_crate_name` features we made, though it doesn't seem like vscode recognizes it.
