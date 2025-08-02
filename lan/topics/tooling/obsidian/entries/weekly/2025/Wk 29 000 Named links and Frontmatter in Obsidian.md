---
status: watch1
---

# 1 Objective

I just want links to have a name, for example tasks spawn other tasks, tasks have goals, etc.

I have found an extension that claims to do this: [Graph Link Types](https://github.com/natefrisch01/Graph-Link-Types).

It needs the data using [DataView](https://github.com/blacksmithgu/obsidian-dataview).

- [x] Figure out how to create frontmatter in obsidian documents
- [x] Get a graph link to work


# 2 Journal

2025-07-20 Wk 29 Sun - 23:08

It already works when I use it like this below...

```
links:: [[Wk 29 002 Tabulating random strings with markers in bash]]

link_with_under_scores:: [[Wk 29 000 Setting up bare git remote repo]]
```

2025-07-20 Wk 29 Sun - 23:19

What gives? It's not working in the other obsidian vault I have!

It seems I can already do `Ctrl+P Add File Property` or `Ctrl+;` to us frontmatter.

Advantage of doing it through frontmatter is that it actually shows up as a property in `Open graph view`.  But how do we filter by it?

[obsidian search docs when clicking on Property (i) icon](https://help.obsidian.md/plugins/search).

According to this, I should just be able to do [has_goal] for instance and find the property has_goal.

If you allow orphans it *does* show up. So the issue is that it doesn't actually preserve the linking.

We can use it for grouping instead of filtering. 

We can disable some groups by ending them with `and false` : `[status:todo] and false`. Especially since groups override one another with priority.

both `\\` and `and false` seem to work until you switch back to the graph view again and find that they're somehow activated again. I guess I have to go more destructive with properties like `[=status:todo]` 

2025-07-20 Wk 29 Sun - 23:50

I see. If you create a note that points nowhere, then links break:

```
I shall point to [[the sun!]]
```

## 2.1 Submitting issue

Submitted issue [#52](https://github.com/natefrisch01/Graph-Link-Types/issues/52). It silently fails when non-existent links are used, and this shouldn't happen.

### 2.1.1 Watch

# 3 Ideas

## 3.1 Use with local graphs

 

This [issue](https://github.com/natefrisch01/Graph-Link-Types/issues/22) mentioned that this could be integrated for local graphs. I also want local graphs since my notes tend to be interconnected at the heading and link level.

A user there also mentioned a plugin that adds created/modified metadata through frontmatter to every note

2025-07-21 Wk 30 Mon - 01:41

Actually when I tried local graph, it's just graph filtered by the current note. I want an actual graph of headings!
