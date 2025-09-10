---
parent: "[[000 Note Repo Migration Sept 8]]"
---

#proj 

Parent: [[000 Note Repo Migration Sept 8]]

# 1 Objective

We need to have an obsidian extension to be able to run commands for task creation automation amongst other things. 

# 2 Related

This is a re-creation of [[002 Create an experimental obsidian extension to test ideas in]].
# 3 Journal

2025-09-10 Wk 37 Wed - 15:26 +03:00

Obsidian documentation can be found on this in [1](https://docs.obsidian.md/Plugins/Getting+started/Build+a+plugin) and [2](https://publish.obsidian.md/hub/04+-+Guides%2C+Workflows%2C+%26+Courses/Guides/How+to+get+started+developing+plugins).

There is a [getting started post](https://dev.to/bjarnerentz/journey-developing-an-obsidian-plugin-part-1-getting-started-53m6) on learnings and also links to [gh obsidianmd/obsidian-sample-plugin](https://github.com/obsidianmd/obsidian-sample-plugin).

2025-09-10 Wk 37 Wed - 15:41 +03:00

So following [obsidian docs build a plugin](https://docs.obsidian.md/Plugins/Getting+started/Build+a+plugin),

Let's create a project from [gh obsidianmd/obsidian-sample-plugin](https://github.com/obsidianmd/obsidian-sample-plugin) template.  Let's just create a `lan-obsidian-plugin` to test things for now. 

It exists: [gh LanHikari22/lan-obsidian-plugin](https://github.com/LanHikari22/lan-obsidian-plugin)

Clone it on my system:

```sh
git clone git@github.com:LanHikari22/lan-obsidian-plugin.git ~/src/cloned/gh/LanHikari22/lan-obsidian-plugin
cd ~/src/cloned/gh/LanHikari22/lan-obsidian-plugin
```

The README mentions to run `npm i` in the repo. It also mentions to check that `node --version` is at least 16, and mine is `v24.1.0` while my `npm --version` is `11.5.2`.

```sh
# in /home/lan/src/cloned/gh/LanHikari22/lan-obsidian-plugin
npm i
npm run dev
```

2025-09-10 Wk 37 Wed - 15:56 +03:00

Seems we need to restart obsidian to see our plugin show up.

```sh
killall obsidian
```

Hi again. Still don't see it... It should be called `obsidian-sample-plugin@1.0.0`

2025-09-10 Wk 37 Wed - 16:44 +03:00

Updated the `manifest.json` with my information and a new plugin name `lan-obsidian-plugin` or `Lan Obsidian Plugin` Updated `package.json` which has the project name also and is what shows up under `npm run dev`.

```sh
# in /home/lan/src/cloned/gh/LanHikari22/lan-obsidian-plugin
npm run dev
```

I might have to copy the manifest and main.js to `/home/lan/src/cloned/gh/LanHikari22/lan-setup-notes/.obsidian/plugins` or similar.

2025-09-10 Wk 37 Wed - 16:52 +03:00

Let's see if a soft link would do:

```sh
# in /home/lan/src/cloned/gh/LanHikari22/lan-setup-notes/.obsidian/plugins
ln -s ~/src/cloned/gh/LanHikari22/lan-obsidian-plugin lan-obsidian-plugin
```

Yup. On a restart it shows up:

![[Pasted image 20250910165345.png]]


It doesn't have many settings yet.

![[Pasted image 20250910165418.png]]

One thing to configure later on would be the categories for big notes.

2025-09-10 Wk 37 Wed - 17:05 +03:00

So `styles.css` should also go there. We should build styles to this if we need to.

(update)
And because of `tsconfig.json` having 
```json
  "include": [
    "**/*.ts"
  ]
```

We can move `main.ts` to `src/main.ts` and structure our ts code differently.

2025-09-10 Wk 37 Wed - 17:42 +03:00

We also had to change this:

```diff
- entryPoints: ["main.ts"],
+ entryPoints: ["src/main.ts"],
```

(/update)

2025-09-10 Wk 37 Wed - 17:19 +03:00

The main thing we want out of this currently is the ability to manipulate our notes and folders on a command. 

Let's start by automating spawning a note. We should be able to do `^P Lan spawn note` and it asks from a menu of available categories the note category, and then it asks for its name. It figures out the triplet ID on its own. Then it does what I'm about to do manually:

1. I wrote "Spawn SPW9" so that I can find that SPW9 and generate a random block identifier at that line later on. It's actually 0 rather than 9, but it doesn't matter. I didn't want this to pop up.
2. I created a new file for the new task.
3. I added frontmatter `parent` and `spawned_by`
4. I added `Parent: [[parent]]`.
5. I added `Spawned in [[this note#^SPW9]]` to generate the random block identifier id
6. I renamed the block identifier from `{rand}` to `spawn-task-{rand}`. This has to be done in both places.
7. I replaced SPW9 with the link to the new task note.
8. I added the new spawned task to the index in the parent.
9. I give the note a `status: todo` and I reflect that in the Index with `**Todo** [[new note]]` in the proper index category.

Spawn [[005 Create Spawn Note Command]] ^spawn-task-ebfb30

Yeah this got very involved and should not be something to do manually. It was much simpler, although still inconvenient before we introduced big notes.