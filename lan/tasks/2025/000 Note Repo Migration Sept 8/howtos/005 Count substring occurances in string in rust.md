---
parent: '[[000 Note Repo Migration Sept 8]]'
spawned_by: '[[013 Change all delta-trace old format notes into note clusters without applying link fixing]]'
context_type: howto
status: done
---

Parent: [000 Note Repo Migration Sept 8](../000%20Note%20Repo%20Migration%20Sept%208.md)

Spawned by: [013 Change all delta-trace old format notes into note clusters without applying link fixing](../tasks/013%20Change%20all%20delta-trace%20old%20format%20notes%20into%20note%20clusters%20without%20applying%20link%20fixing.md)

Spawned in: [<a name="spawn-howto-a57747" />^spawn-howto-a57747](../tasks/013%20Change%20all%20delta-trace%20old%20format%20notes%20into%20note%20clusters%20without%20applying%20link%20fixing.md#spawn-howto-a57747)

# 1 Journal

2025-10-07 Wk 41 Tue - 06:02 +03:00

This [stackoverflow answer](https://stackoverflow.com/a/68970698/6944447) uses a windowing solution, which is cool because I have not used windows with iterators!

Refining my solution from

````rust
pub fn count_substrings(s: &str, patt: &str) -> usize {
    let mut mut_i = 0;
    let mut mut_count = 0;

    while mut_i < s.len() {
        if mut_i + patt.len() > s.len() {
            break;
        }

        let segt = &s[mut_i..mut_i + patt.len()];

        if segt == patt {
            mut_count += 1;
            mut_i += patt.len();
        } else {
            mut_i += 1;
        }
    }

    mut_count
}
````

To

````rust
pub fn count_substrings(s: &str, patt: &str) -> usize {
    s
        .as_bytes()
        .windows(patt.len())
        .filter(|&w| w == patt.as_bytes())
        .count()
}
````

2025-10-07 Wk 41 Tue - 06:09 +03:00

This [stackoverflow answer](https://stackoverflow.com/a/68971945/6944447) mentions use of [wiki Aho–Corasick algorithm](https://en.wikipedia.org/wiki/Aho%E2%80%93Corasick_algorithm) for linear time results which is more optimal as the string gets larger. This is through the crate [docs.rs aho_corasick](https://docs.rs/aho-corasick/latest/aho_corasick/)
