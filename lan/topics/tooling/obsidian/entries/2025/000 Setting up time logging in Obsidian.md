---
external:
---
#lan #tooling #obsidian #time #logging #external


![[Pasted image 20250622183338.png]]

# 1 Objective

To be able to have an automatic start/stop time logging functionality with events and summary. 

It should:
- [x] Be easy to use. Just Start/Stop with the cursor pointing to beginning of the note or to some header. It should be able to differentiate based on this if it's a note-wide log or a note#header log. 
- [x] Be collaborative, multiple people should be able to use it in the same vault
- [x] Generate weekly event log files automatically
- [x] Be able generate summary reports
- [x] Be able to accumulate all time events from headers to the note.
- [x] Link back to the notes/headers so that it can be used to navigate context in time.
- [x] Be able to use other links too and not just headers.
- [ ] Be resistant to changes in link names and renaming files.

# 2 Journal

## 2.1 Setup

2025-06-22 Wk 25 Sun - 20:14

To satisfy this, I created scripts in `scripts/templater/` to do this: 
- `start_log.js`, `stop_log.js`, `summarize_time_logs.js`

Each require an identity input, and are configured via `md` template note commands `Lan Start Log`, `Lan Stop Log`, `LAd Summarize Time Logs` respectively.

The time reports go to `scripts/template/data/{identity}` respectively, in my case `lan` for `{identity}`. 

With identity differentiated, multiple people can use this tool. They have to add their own
- `data/{identity}` and 
- `{Identity} Start Log.md`, 
- `{Identity} Stop Log.md` 
- `{Identity} Open Timeline Log.md`
- `{Identity} Summarize Time Logs.md`.

If this become cumbersome or is done frequently, it can be automated via a script.

Then this can be used via `Ctrl+P Templater: Open insert template modal`  and selecting the command. A Hotkey could also be used for opening the templater commands, like `Alt+E`

You will need the plugins:
- Templater
- Super Simple Time Tracker

Templater should be configured, 
- `Script files folder location` should be set to where `{identity} Start Log` would go, in this case `scripts/templater`
-  `Template folder location`  should be set to `scripts/templater/commands/` to properly filter the list of available commands on Alt+E. 

# 3 Changes

2025-06-23 Wk 26 Mon - 14:55

A new command, `{Identity} Open Timeline Log` was added for quick jump to the latest weekly timeline. See [[Wk 26 000 Add script for opening obsidian note quickly]] for context.

2025-06-23 Wk 26 Mon - 17:16

Added a script `copy_templater_scripts_setup.sh` for ease of copying over vaults while in development. It updates all the system files and defined identity commands like `{Identity} Start Log.md` but not `data/` directories.

2025-06-23 Wk 26 Mon - 17:31

Now setting  `Template folder location`  to `scripts/templater/commands/` to provide proper list of available templater commands.

2025-07-17 Wk 29 Thu - 23:02

Made the scripts themselves in typescript to generate the js files automatically via `esbuild`. Check out `build.sh` in `scripts/templater/`.

The identity-based commands can now be automatically generated via 

```sh
./scripts/templater/create_commands_for_user.sh "User UI Name" "username"
```

Then when opening the command menu like with Alt+K+E

These can all be undone with 

```sh
rm scripts/templater/commands/User*
```

in this case since all the commands use the UI username.

It also creates a folder under 

```sh
./scripts/templater/data/$username
```

2025-09-17 Wk 38 Wed - 13:53 +03:00

Updated summaries so that there's no "all" summary, but many total summary files by root item. This way we can refer to total time taken on a specific note or note cluster.

# 4 Some Issues

2025-09-10 Wk 37 Wed - 05:27 +03:00

Notes from [[004 Parse parent frontmatter property for time log summaries]] to look into the timezones set up for the time logger.

# 5 Some Ideas

2025-06-22 Wk 25 Sun - 20:31

This could turn into a plugin or an enhancement to Super Simple Time Tracker

# 6 External

2025-07-18 Wk 29 Fri - 00:14

This is referenced in README for `scripts/templater/`.