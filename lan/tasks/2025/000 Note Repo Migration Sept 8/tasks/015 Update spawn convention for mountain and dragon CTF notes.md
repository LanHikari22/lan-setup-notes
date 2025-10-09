---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[013 Change all delta-trace old format notes into note clusters without applying link fixing]]"
context_type: task
status: done
---

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[013 Change all delta-trace old format notes into note clusters without applying link fixing]]

Spawned in: [[013 Change all delta-trace old format notes into note clusters without applying link fixing#^spawn-task-09e1ca|^spawn-task-09e1ca]]

# 1 Objective

This is for the document `lan/topics/practice/ctf/topic/entries/2025/000 Solving Mountain and Dragon CTF.md` 

It has varying conventions of spawning which should be made uniform for ease of migration.

# 2 Journal

2025-10-07 Wk 41 Tue - 06:48 +03:00

It seems part of the very early creation of this spawn format, since it wasn't even using random ids:

```
## 5.8 Javascript execute some code every second on interval

- [x]

From [[#^spawn-howtos-js-on-interval]].
```

For this instance, no explicit spawning statement was even found, besides just the block identifer.

It's updated to

```

## 5.8 Javascript execute some code every second on interval

- [x]

From [[#^spawn-howto-8a31a9]] in [[#3.9 Create a driver to search for death and infinity strings]]
```

with an explicit spawn added

```
2025-08-02 Wk 31 Sat - 07:09

Adding `/watch {addr16}` and `/nowatch {addr16}`. This allows the value to be pinged every second to the web console. If it's unwatched with `nowatch`, then it will stop pinging. 

Spawn [[#5.8 Javascript execute some code every second on interval]] ^spawn-howto-8a31a9
```

This is so that in migration they are uniform and can be parsed and migrated to the new protocol.

2025-10-07 Wk 41 Tue - 07:01 +03:00

And in the same document, we start seeing the random ids appearing with the category type coming after `spawn-`. 

```
## 4.4 Tape reaches invalid command 51 at 0x0b89

- [x]

From [[#^spawn-issue-0b89]].

2025-08-01 Wk 31 Fri - 07:18
```

Still in there we would just put the spawning together with an explanation instead of being its own spawn paragraph.

```
2025-08-01 Wk 31 Fri - 07:06

We seem to end up at an invalid command 51. This is a common data byte... See [[#4.4 Tape reaches invalid command 51 at 0x0b89|issue]]. ^spawn-issue-0b89
```

Changing it to

```
We seem to end up at an invalid command 51. This is a common data byte... See issue below.

Spawn [[#4.4 Tape reaches invalid command 51 at 0x0b89]] ^spawn-issue-0b89
```

These spawns match our current format, the only thing that needs to be updated is just the link.

Changing 

```
From [[#^spawn-issue-0b89]].
```

to

```
From [[#^spawn-issue-0b89]] in [[#3.8 Reconstruct labels in the tape program]]
```

2025-10-07 Wk 41 Tue - 07:12 +03:00

Since we made `Reconstructing tape content` its own task, it's now the case that a later count task is spawning an earlier count task... 

```
Spawn [[#3.7 Handle loaded param16 flag automatically in commands when reconstructing tape program]] ^spawn-task-781da8
```

- [ ] This should be fixed after extraction by renumbering it to be first and renumbering all others

This is easier to be done with the new format, because obsidian will be able to propagate these link changes, but we will still have to worry about changing them in JSON blobs for the timeline...

Added to [[003 Objective reminders noted during sept 8 migration]]

2025-10-07 Wk 41 Tue - 07:26 +03:00

```
2025-07-30 Wk 31 Wed - 12:36

An option:
- [zeromq typescript library](https://www.npmjs.com/package/zeromq) [(on github.io)](https://zeromq.github.io/zeromq.js/).

Trying to run the pub/sub example there.

Looking into [[#4.2 Errors while attempting to import zeromq for non-node platform|zeromq import issue]].
```

Implicit spawns... At least they're recoverable. There's a lot of records with just no spawns.

2025-10-07 Wk 41 Tue - 07:33 +03:00

```sh
# in /home/lan/src/cloned/gh/deltatraced/delta-trace
git commit -m "update spawn convention for mountain and dragon CTF"

# out
[main 22fdaee] update spawn convention for mountain and dragon CTF
 2 files changed, 993 insertions(+), 968 deletions(-)
 delete mode 100644 Pasted image 20251003052918.png.md
```

OK