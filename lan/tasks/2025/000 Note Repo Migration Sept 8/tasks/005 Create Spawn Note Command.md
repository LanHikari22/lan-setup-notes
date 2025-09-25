---
parent: "[[000 Note Repo Migration Sept 8]]"
spawned_by: "[[000 Create Obsidian extension for actions]]"
context_type: task
status: todo
---
#obsidian

Parent: [[000 Note Repo Migration Sept 8]]

Spawned by: [[000 Create Obsidian extension for actions]]

Spawned in [[000 Create Obsidian extension for actions#^spawn-task-ebfb30|^spawn-task-ebfb30]]

# 1 Journal

2025-09-10 Wk 37 Wed - 17:38 +03:00

We'll be testing this here, to see if we can easily spawn new commands.

Seems I have to do `^P Reload app without saving` to get changes to register.

2025-09-10 Wk 37 Wed - 18:02 +03:00

We added a currently empty command

```ts
this.addCommand({
	id: "spawn-note",
	name: "Spawn Note",
	editorCallback: spawn_note_command.run,
});
```

2025-09-10 Wk 37 Wed - 18:15 +03:00

The base information of interest to us is
- The current note, and its content
- The current cursor position in that note
- The folder the note belongs to, and possibly the folder that belongs to
- Ability to edit the content of notes in those folders
- Ability to add a category folder if it doesn't exist
- Ability to add a new note to a folder relative to our current folder

API information can be found at [gh obsidianmd/obsidian-api](https://github.com/obsidianmd/obsidian-api) and on the [obsidian developer docs](https://docs.obsidian.md/Home)

There is documentation on [replaceSelection and getSelection](https://docs.obsidian.md/Plugins/Editor/Editor#Replace+current+selection) that were used in the example.

We're interested in [Editor.getCursor](https://docs.obsidian.md/Reference/TypeScript+API/Editor/getCursor) and [Editor.getLine](https://docs.obsidian.md/Reference/TypeScript+API/Editor/getLine)

getCursor gives us an [EditorPosition](https://docs.obsidian.md/Reference/TypeScript+API/EditorPosition) whose line propery can be given to getLine to give us the current line the cursor is at.

We'll log with `console.log`, just do `Ctrl+Shift+I` and undock

![[Pasted image 20250910184323.png]]

2025-09-10 Wk 37 Wed - 19:38 +03:00

Looking at more API code found previously in scripts by LLMs:

There's [tp.app.vault.adapter](https://docs.obsidian.md/Reference/TypeScript+API/Vault/adapter) which gives us [DataAdapter](https://docs.obsidian.md/Reference/TypeScript+API/DataAdapter) that can be used to create directories, view directories, write content to files...

[read](https://docs.obsidian.md/Reference/TypeScript+API/DataAdapter/read) for example returns a promise, so we need to `await`.

2025-09-10 Wk 37 Wed - 20:15 +03:00

Here are some traces

```ts
import { Editor, MarkdownView } from 'obsidian';

export async function run(editor: Editor, view: MarkdownView) {
    console.log(`(Spawn)`)

    const opt_file = view.file;

    if (opt_file) {
        console.log(`file path: ${opt_file.path}`);
        console.log(`file name: ${opt_file.name}`);
        console.log(`file parent: ${opt_file.parent}`);
        console.log(`file stat: ${opt_file.stat}`);
        console.log(`file vault adapter name: ${opt_file.vault.adapter.getName()}`);

        console.log(`cache: ${view.app.metadataCache.getFileCache(opt_file)}`);

        const content = await opt_file.vault.read(opt_file);

        console.log(`content: ${content}`);

        const opt_other_file = opt_file.vault.getMarkdownFiles().find(f => f.basename === "000 Setting up time logging in Obsidian");
        if (opt_other_file) {
            console.log(`other_file path ${opt_other_file.path}`)
        }
    }


    const cursor = editor.getCursor();
    const current_line = editor.getLine(cursor.line);

    console.log(`cursor: ${cursor}`);
    console.log(`current_line: ${current_line}`);

    console.log(`doc: ${editor.getDoc()}`);

    console.log(`(/Spawn)\n\n`)
}
```

```
(Spawn)
file path: lan/tasks/2025/000 Note Repo Migration Sept 8/tasks/005 Create Spawn Note Command.md
file name: 005 Create Spawn Note Command.md
file parent: [object Object]
file stat: [object Object]
file vault adapter name: lan-setup-notes
cache: [object Object]
content: {this file's text}
other_file path lan/topics/tooling/obsidian/entries/2025/000 Setting up time logging in Obsidian.md
cursor: [object Object]
current_line: getCursor gives us an [EditorPosition](https://docs.obsidian.md/Reference/TypeScript+API/EditorPosition) whose line propery can be given to getLine to give us the current line the cursor is at.
doc: [object Object]
(/Spawn)
```

A lot of those say [object Object] so we should view them as JSON dictionaries.

2025-09-10 Wk 37 Wed - 20:29 +03:00

Using `JSON.stringify` on these objects causes an error `Converting circular structure to JSON`:

```ts
const opt_file = view.file;

if (opt_file) {
	console.log(`file parent: ${JSON.stringify(opt_file.parent)}`);
}
	
console.log(`doc: ${JSON.stringify(editor.getDoc())}`);
```

The cache gives us some information about the frontmatter properties as well as tags and other things like getting heading positions.

```ts
const opt_file = view.file;

if (opt_file) {
	console.log(`cache: ${JSON.stringify(view.app.metadataCache.getFileCache(opt_file))}`);
}
```

Some selective output:

```
tags":[{"position":{"start":{"line":5,"col":0,"offset":149},"end":{"line":5,"col":9,"offset":158}},"tag":"#obsidian"}]

"frontmatterLinks":[{"key":"parent","link":"000 Note Repo Migration Sept 8","original":"[[000 Note Repo Migration Sept 8]]","displayText":"000 Note Repo Migration Sept 8"},{"key":"spawned_by","link":"000 Create Obsidian extension for actions","original":"[[000 Create Obsidian extension for actions]]","displayText":"000 Create Obsidian extension for actions"}]
```

2025-09-10 Wk 37 Wed - 20:44 +03:00

For the cursor,

```ts
console.log(`cursor: ${JSON.stringify(cursor)}`);
```

```
cursor: {"line":47,"ch":192}

```

192 points to the index of the last character in the line. That line happens to be 193 characters long, and I took this trace with my cursor at the very end of the line.

We can get modification timestamps:

```ts
const opt_file = view.file;

if (opt_file) {
	console.log(`file stat: ${JSON.stringify(opt_file.stat)}`);
}
```

```
file stat: {"ctime":1757514159040,"mtime":1757526398374,"size":5764}
```

2025-09-10 Wk 37 Wed - 20:51 +03:00

via LLM suggestion we can use this to view the circular objects:

```ts
const opt_file = view.file;

if (opt_file) {
	console.dir(opt_file.parent, { depth: null });
}

console.dir(editor.getDoc(), { depth: null});
```

Those are big objects.

2025-09-10 Wk 37 Wed - 21:44 +03:00

Besides data and folder structure, we need to figure out how to prompt the user for input. We need them to write both the category (preferably to select it from a menu) and to write the name of the new note.

There's [SuggestModal](https://docs.obsidian.md/Reference/TypeScript+API/SuggestModal) and [FuzzySuggestModal](https://docs.obsidian.md/Reference/TypeScript+API/FuzzySuggestModal)

[obsidian forum post](https://forum.obsidian.md/t/prompt-user-input-api/36893/2) gives a link, but it's to migrated documentation and no longer points to where it should.  But it should be called `#accept-user-input`, which we can find in [obsidian docs Modals: Accept user input](https://docs.obsidian.md/Plugins/User+interface/Modals#Accept+user+input).

2025-09-10 Wk 37 Wed - 22:41 +03:00

User input is setup!

![[Pasted image 20250910224147.png]]

![[Pasted image 20250910224218.png]]

![[Pasted image 20250910224246.png]]


![[Pasted image 20250910224348.png]]

And finally the logic sets off once we have all user input!

2025-09-10 Wk 37 Wed - 21:01 +03:00

So when spawning a new small note, we should start by examining the folder structure. We need to be in a big note folder structure. Meaning that, the current file is the index file, or we are a small note in a valid category folder under a big note folder. We should give an error otherwise.

There is [Notice](https://docs.obsidian.md/Reference/TypeScript+API/Notice) for information messages. Don't see anything for other sorts of messages, so we can give an error on this (and the console).

2025-09-10 Wk 37 Wed - 23:04 +03:00

For a capability test, we need to be able to add content at the next line from the cursor on user invocation.

From [obsidian docs Insert text at cursor position](https://docs.obsidian.md/Plugins/Editor/Editor#Insert+text+at+cursor+position), 

We can see it's just a matter of replacing range at just the cursor position. For it to be a new line, maybe we can just add a new line character at the cursor position.

2025-09-10 Wk 37 Wed - 23:15 +03:00

[stackoverflow answer for random hex string](https://stackoverflow.com/a/1349426/6944447).

2025-09-11 Wk 37 Thu - 00:11 +03:00

(update)
I use `throw new Error("unimplemented");` for functions I've not written yet.

2025-09-11 Wk 37 Thu - 16:30 +03:00

To make it easier to use, added it as a vscode snippet in `/home/lan/.config/Code/User/snippets/typescript.json`

```json
"Unimplemented": {
	"prefix": "unimplemented",
	"body": [
		"throw new Error(\"unimplemented\");",
	],
	"description": "Unimplemented"
}
```

(/update)

2025-09-11 Wk 37 Thu - 01:33 +03:00

The implementation now should handle spawning a new small note and building a dual brindge, as well as all the frontmatter details. What remains after testing this is updating the index. 

2025-09-11 Wk 37 Thu - 01:40 +03:00

Now testing.

```
 Error: Could not find file for link [[000 Note Repo Migration Sept 8]]
 Error: Could not retrieve frontmatter note property parent for 005 Create Spawn Note Command.md
 Error: Could not get big note index file for 005 Create Spawn Note Command.md
```

It preserved the `[[ ]]`. Need to remove those.

2025-09-11 Wk 37 Thu - 01:51 +03:00

It fails to generate triplet IDs unless the context type folder exists. Need to create it if it doesn't.

2025-09-11 Wk 37 Thu - 01:58 +03:00

Just have to add `.md` to the generated file. It created it, but without extension. Other minor issue is that it said `spawn-inferences`, when it should be a small code `infer` for the block identifier.

2025-09-11 Wk 37 Thu - 02:07 +03:00

It works! All that remains is updating the index. We add a `status: todo` frontmatter property by default to spawned items unless they are an entry, an idea, or an inference. `status: {status}` this `{status}` needs to be captured in the index as `**{status}** {note}`. 

2025-09-11 Wk 37 Thu - 02:15 +03:00

Awesome! We can spawn much more freely now! Let's tackle the last item!

Spawn [[006 Regenerate cluster core note index heading]] ^spawn-task-d5eb50

2025-09-11 Wk 37 Thu - 02:36 +03:00

We're also able to spawn from the index file! One more thing, the shown categories should be singular. Not Entries, but Entry, for example. We're spawning just one note.

2025-09-11 Wk 37 Thu - 02:43 +03:00

Names changed! And made the string conversion general since there's many of them.

Spawn [[003 Rename github origin master branch to main]] ^spawn-howto-ea5401

2025-09-11 Wk 37 Thu - 17:38 +03:00

Input handling accepts `]]` and then results in issues.

Spawn [[007 Add some note name sanitization for spawn note]] ^spawn-task-b8adc5


