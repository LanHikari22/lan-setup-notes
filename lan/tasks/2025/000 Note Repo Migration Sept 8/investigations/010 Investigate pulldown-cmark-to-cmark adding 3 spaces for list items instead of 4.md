---
parent: '[[000 Note Repo Migration Sept 8]]'
spawned_by: '[[011 Create integration tests for obsidian patch fixes for sept 8 migration]]'
context_type: investigation
status: done
---

Parent: [000 Note Repo Migration Sept 8](../000%20Note%20Repo%20Migration%20Sept%208.md)

Spawned by: [011 Create integration tests for obsidian patch fixes for sept 8 migration](../tasks/011%20Create%20integration%20tests%20for%20obsidian%20patch%20fixes%20for%20sept%208%20migration.md)

Spawned in: [<a name="spawn-invst-52493f" />^spawn-invst-52493f](../tasks/011%20Create%20integration%20tests%20for%20obsidian%20patch%20fixes%20for%20sept%208%20migration.md#spawn-invst-52493f)

# 1 Journal

2025-09-30 Wk 40 Tue - 03:47 +03:00

This is an [example](https://github.com/Byron/pulldown-cmark-to-cmark/blob/bf34a3cac68e6f82a24ee3d44224a9e2ef2bcd0d/src/lib.rs#L566C41-L566C45) from [gh Byron/pulldown-cmark-to-cmark](https://github.com/Byron/pulldown-cmark-to-cmark/tree/main) where they hardcode adding three spaces `"   "`.

I found it being done for code blocks, and [footnote definition](https://github.com/Byron/pulldown-cmark-to-cmark/blob/bf34a3cac68e6f82a24ee3d44224a9e2ef2bcd0d/src/lib.rs#L513).

2025-09-30 Wk 40 Tue - 04:10 +03:00

We're trying to find why it's adding 3 spaces for nested list bullets.

````
[2025-09-30T01:16:47Z TRACE migration_rs::common] <old_content>
    1. Apples
        - Come in many colors
    2. Fruit
        - Come in many flavors
    </old_content>
[2025-09-30T01:16:47Z TRACE migration_rs::common] <new_content>
    3. Apples
       - Come in many colors
    2. Fruit
       - Come in many flavors
    </new_content>

<events>
Start(List(Some(1)))
Start(Item)
Text(Borrowed("Apples"))
Start(List(None))
Start(Item)
Text(Borrowed("Come in many colors"))
End(Item)
End(List(false))
End(Item)
Start(Item)
Text(Borrowed("Fruit"))
Start(List(None))
Start(Item)
Text(Borrowed("Come in many flavors"))
End(Item)
End(List(false))
End(Item)
End(List(true))
</events>
````

No sign for `"   "` added to texts in their [Text variant](https://github.com/Byron/pulldown-cmark-to-cmark/blob/bf34a3cac68e6f82a24ee3d44224a9e2ef2bcd0d/src/lib.rs#L835).

2025-09-30 Wk 40 Tue - 04:22 +03:00

From [Start(Item)](https://github.com/Byron/pulldown-cmark-to-cmark/blob/bf34a3cac68e6f82a24ee3d44224a9e2ef2bcd0d/src/lib.rs#L402) we can expect a space after the list token `*`/`-`, but still no mention of the spaces before it.

[fn list_item_padding_of](https://github.com/Byron/pulldown-cmark-to-cmark/blob/bf34a3cac68e6f82a24ee3d44224a9e2ef2bcd0d/src/text_modifications.rs#L74) Seems to `n` and pads that many spaces + 2? For some reason they have a period there with a space: `{n}. `. We know the period is because `n` really means the bullet number, like `3.`.  I guess it just needs to know how much spacing something like `2. ` and `125. ` take, but this is still not affecting the spaces *before* the item.

And they do nothing for the [start of a list](https://github.com/Byron/pulldown-cmark-to-cmark/blob/bf34a3cac68e6f82a24ee3d44224a9e2ef2bcd0d/src/lib.rs#L586). And nothing relevant for [end of item/list](https://github.com/Byron/pulldown-cmark-to-cmark/blob/bf34a3cac68e6f82a24ee3d44224a9e2ef2bcd0d/src/lib.rs#L797).

2025-09-30 Wk 40 Tue - 04:46 +03:00

They explain the meaning of [padding](https://github.com/Byron/pulldown-cmark-to-cmark/blob/bf34a3cac68e6f82a24ee3d44224a9e2ef2bcd0d/src/lib.rs#L60) in `pub struct State`, saying it's printed after each new line.

If we assume that [fn list_item_padding_of](https://github.com/Byron/pulldown-cmark-to-cmark/blob/bf34a3cac68e6f82a24ee3d44224a9e2ef2bcd0d/src/text_modifications.rs#L74) is called with `l` being `None` since this is not a numbered bullet, this would say that two spaces should be added. But if it were a numbered bullet, it might add 3 spaces given that a single digit takes a space, a dot takes a space, and with the extra space, we have 3: `1. ` $\to$ `   ` . It could have inherited 3-spaces from the *previous* numbered list.

To confirm this, we need to see what happens when we cross the double digits in numbered bullets. Let's add a test case `list-002`

2025-09-30 Wk 40 Tue - 05:01 +03:00

The test case `list-002` seems to confirm it.

````
[2025-09-30T02:00:08Z TRACE migration_rs::common] <old_content>
    1. Item
        - Desc
    2. Item
    3. Item
    4. Item
    5. Item
    6. Item
    7. Item
    8. Item
    9. Item
    10. Item
        - Desc
    11. Item
    12. Item
    </old_content>
````

````
[2025-09-30T02:00:08Z TRACE migration_rs::common] <new_content>
    1. Item
       - Desc
    2. Item
    3. Item
    4. Item
    5. Item
    6. Item
    7. Item
    8. Item
    9. Item
    10. Item
        - Desc
    11. Item
    12. Item
    </new_content>
````

Notice that `- Desc` gets only 3 space padding for

2025-09-30 Wk 40 Tue - 05:11 +03:00

And if you change the number of spaces for Item 10 description from 4 to 3, it adds news lines before and after.

````
[2025-09-30T02:10:44Z TRACE migration_rs::common] <old_content>
    1. Item
        - Desc
    2. Item
    3. Item
    4. Item
    5. Item
    6. Item
    7. Item
    8. Item
    9. Item
    10. Item
       - Desc
    11. Item
    12. Item
    </old_content>
````

````
[2025-09-30T02:10:44Z TRACE migration_rs::common] <new_content>
    1. Item
       - Desc
    2. Item
    3. Item
    4. Item
    5. Item
    6. Item
    7. Item
    8. Item
    9. Item
    10. Item

    - Desc

    11. Item
    12. Item
    </new_content>
````

````
// Events on [4, 7] spaces for Item 10 Desc
// Events on 1 '\t' for Item 10 Desc
Start(Item)
Text(Borrowed("Item"))
Start(List(None))
Start(Item)
Text(Borrowed("Desc"))
End(Item)
End(List(false))
End(Item)

// Events on 3 spaces for Item 10 Desc
Start(Item)
Text(Borrowed("Item"))
End(Item)
End(List(true))
Start(List(None))
Start(Item)
Text(Borrowed("Desc"))
End(Item)
End(List(false))

// Events on 8 spaces for Item 10 Desc
Start(Item)
Text(Borrowed("Item"))
SoftBreak
Text(Borrowed("- Desc"))
End(Item)
````

So [gh pulldown-cmark/pulldown-cmark](https://github.com/pulldown-cmark/pulldown-cmark) is itself very particular about the number of spaces added there, even before any rendering.

2025-09-30 Wk 40 Tue - 05:46 +03:00

[spec CommonMark list-items](https://spec.commonmark.org/0.31.2/#list-items) specifies the rules and mentions about items having to at least include the width of the list marker $W$.
