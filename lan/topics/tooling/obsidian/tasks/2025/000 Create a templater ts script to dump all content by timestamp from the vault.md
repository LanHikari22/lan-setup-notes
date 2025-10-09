---
status: todo
---

# 1 Objective

I use timestamps like

2025-08-01 Wk 31 Fri - 03:27

It could be be fun to have a temporal view of my vault.

Basically the rules are, everything after a timestamp belongs to it until

1. We hit another timestamp; or
1. We hit a heading

With this we can represent a lot of content as timestamp chunks.

We can also generate a graph of headings and traverse them with temporal arrows where

````
A --> |WkNN HH:MM| B --> |WkNN HH:MM| C --> |WkNN HH:MM| A
````

would indicate the week number and time in the transitions themselves.

Do not include A->A links.
