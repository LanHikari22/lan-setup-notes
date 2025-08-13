
# 1 Objective

# 2 Journal

2025-08-13 Wk 33 Wed - 10:01

First we need to reproduce this issue. 

Spawn [[#3.1 File an issue for the broken links]] ^spawn-task-a5bc4a

# 3 Tasks

## 3.1 File an issue for the broken links

- [ ] 

### 3.1.1 Broken file-internal link

2025-08-13 Wk 33 Wed - 10:01

From [[#^spawn-task-a5bc4a]].

Basically the links used here, once we export this repository, we will not be able to click.

In the [raw webview export of this note](https://raw.githubusercontent.com/LanHikari22/lan-setup-notes/refs/heads/webview/lan/topics/tooling/obsidian/tasks/2025/004%20Fix%20obsidian%20export%20to%20support%20internal%20links.md) (this always updates), 

```
From [^spawn-task-a5bc4a](004%20Fix%20obsidian%20export%20to%20support%20internal%20links.md#spawn-task-a5bc4a).
```

### 3.1.2 Broken Embedded text links

Also, embedded note content does not work like

![[003 Making obsidian repositories appear nicer in browser#^ebe248]]

This should somehow display the snippet I selected from the other note, but currently it does not.

I should see:

![[Pasted image 20250813102755.png]]

What I see:

The entire note being embedded:

![[Pasted image 20250813102841.png]]

In obsidian, the link itself looks like

![[Pasted image 20250813102914.png]]

It's a generated code for a section I wanted to pull out from the other note.

In the [raw webview export of this note](https://raw.githubusercontent.com/LanHikari22/lan-setup-notes/refs/heads/webview/lan/topics/tooling/obsidian/tasks/2025/004%20Fix%20obsidian%20export%20to%20support%20internal%20links.md) (this always updates), 

```markdown
Also, embedded note content does not work like

# 1 Objective

As is, repositories will not load image,s embedded links nor internal links. So it makes the obsidian view diverge a lot from the web view. Since we provide URL links to notes, it would be good for the views to be more compatible.

# 2 Journal

## 2.1 Outstanding issues
[...]
```

It just copied the entire note text.

### 3.1.3 Comparison with how I made valid README internal file links

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

### 3.1.4 Case 1: Basic mapping and filtering

### 3.1.5 Pend

# 4 Issues

# 5 HowTos

# 6 Investigations

# 7 Ideas

# 8 Side Notes
# 9 External Links

# 10 References