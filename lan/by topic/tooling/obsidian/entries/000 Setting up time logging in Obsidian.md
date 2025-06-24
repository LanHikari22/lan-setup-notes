#lan #tooling #obsidian #time #logging #external


![[Pasted image 20250622183338.png]]

# 1 Objective

To be able to have an automatic start/stop time logging functionality with events and summary. 

It should:
1. Be easy to use. Start/Stop with cursor at the note beginning or at some header, and reflect that header.
2. Be collaborative, multiple people should be able to use it in the same vault
3. Generate weekly event log files automatically
4. Be able generate summary reports
5. Be able to accumulate all time events from headers to the note.

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

# 4 Ideas

2025-06-22 Wk 25 Sun - 20:31

## 4.1 This could turn into a plugin or an enhancement to Super Simple Time Tracker