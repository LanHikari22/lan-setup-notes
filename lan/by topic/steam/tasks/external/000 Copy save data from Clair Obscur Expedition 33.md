#lan #entry #steam #gaming #done #resolved #external 

2025-07-01 Wk 27 Tue - 13:44

# 1 Objective

I am doing a fun challenge, and if I do decide to pass on and level up and stuff I wouldn't be able to do it again. I would like to

- [x] preserve my save data so that I can recover this at any point

# 2 Solution

After disabling cloud saving [[#^fig-steam-cloud-save]] (although unsure if this is necessary),

We are able to find the file [[#^script-search-sys-for-sav|here]]:

```sh
find / -type f -name "*.sav" 2>/dev/null | less

# output
[...]
/media/lan/parti2/Steam/steamapps/compatdata/1903340/pfx/drive_c/users/steamuser/AppData/Local/Sandfall/Saved/SaveGames/<unk_id>/EXPEDITION_0.sav
[...]
```

So let's copy that over

```sh
mkdir -p ~/data/backups/2025-07-01-expedition-save-data
cp -r /media/lan/parti2/Steam/steamapps/compatdata/1903340/pfx/drive_c/users/steamuser/AppData/Local/Sandfall/Saved/SaveGames/<unk_id>/EXPEDITION_0.sav ~/data/backups/2025-07-01-expedition-save-data
cp -r /media/lan/parti2/Steam/steamapps/compatdata/1903340/pfx/drive_c/users/steamuser/AppData/Local/Sandfall/ ~/data/backups/2025-07-01-expedition-save-data 
```

If we want to play with the file or explore it:

```sh
mkdir -p ~/data/re/expedition-sav/
cp -r ~/data/backups/2025-07-01-expedition-save-data/EXPEDITION_0.sav ~/data/re/expedition-sav
```

# 3 Journal

## 3.1 Finding the ID via the steam store page

2025-07-01 Wk 27 Tue - 13:52

In [steam forum: on save data](<https://steamcommunity.com/discussions/forum/1/1643170269579547670/>) [[#^1]],

[.357](<https://steamcommunity.com/id/stealth-mass02>) says that we can also find a game's ID by inspecting it on the steam store. 

[[Wk 25 001 Steam not starting CoD in Ubuntu Linux#0.1.3 Let's first discover what its Steam ID is|Previously]] we found it via the terminal But we can also inspect the steam page:

https://store.steampowered.com/agecheck/app/1903340/

And conclude the ID is 1903340.

## 3.2 The save data location

2025-07-01 Wk 27 Tue - 14:11

[.357](<https://steamcommunity.com/id/stealth-mass02>) also mentions it should be stored here:

```
<Steam-folder>/steamapps/compatdata/{game's app ID}/pfx
```

So in my case

```sh
ls /home/lan/snap/steam/common/.local/share/Steam/steamapps/compatdata/1903340/pfx
```

But this is a big folder. It has a whole `drive_c`. It's not specific enough.

2025-07-01 Wk 27 Tue - 14:18

It seems every game has its own scheme for saving data. It's not standardized.

In [post on clair obscur expedition 33 save location](<https://primagames.com/tips/clair-obscur-expedition-33-save-file-location>) [[#^2]], 

```
C:\Users\**[Windows Username]**\AppData\Local\Sandfall\Saved\**SaveGames**
```

Expected as `EXPEDITION_0.sav`. 

But we do not find a `Sandfall` in 

```sh
ls /home/lan/snap/steam/common/.local/share/Steam/steamapps/compatdata/1903340/pfx/drive_c/users/steamuser/AppData/Local

# output
Microsoft  Temp
```

2025-07-01 Wk 27 Tue - 14:25

Let's try to look for a `*.sav`:

```sh
find -L /home/lan/snap/steam/common/.local/share/Steam/steamapps/compatdata/1903340 -maxdepth 10 -type f 2>/dev/null | grep 'EXPEDITION_0.sav'

# output
[Nothing]
```

2025-07-01 Wk 27 Tue - 14:35

In Steam Library page for `Clair Obscur: Expedition 33` Settings>General, 

![[Pasted image 20250701143618.png]]
^fig-steam-cloud-save

It is configured to save the game to the cloud. I disabled this to see if I can generate the sav file.

2025-07-01 Wk 27 Tue - 15:13

Steam cloud data can also be found [here](<https://store.steampowered.com/account/remotestorage>).

Specifically for `Clair Obscur: Expedition 33`, [here](<https://store.steampowered.com/account/remotestorageapp/?appid=1903340>).

The last time it saved to the cloud ther seems to be Jun 22? It's Jul 1 though: `Sandfall/Saved/SaveGames/<unk_id>/Backup/EXPEDITION_0_2025_6_22_16_9_26.sav`

It says this is in `WinAppDataLocal`. 

Despite turning off cloud saving, I still cannot find saves or `Sandfall` in `/home/lan/snap/steam/common/.local/share/Steam/steamapps/compatdata/1903340/pfx/drive_c/users/steamuser/AppData/Local`

## 3.3 Trying to trace logs on game save trigger

2025-07-01 Wk 27 Tue - 15:29

[[Wk 25 000 Steam says Game started but nothing happens#1.1.2.6 Getting all logs from Snap Steam|Previously]], we used this to trigger on many stream logs being written:

```sh
inotifywait -m -r -e modify -e create -e delete \
  /home/lan/snap/steam/common/.local/share/Steam/ |
  while read path action file; do
    echo "$(/bin/date) - $action on $path$file"
  done
```


All we find that changes is when you Alt+Tab onto the game, the following files get modified:

```
Tue Jul  1 03:35:21 PM +03 2025 - MODIFY on /home/lan/snap/steam/common/.local/share/Steam/logs/controller_ui.txt
Tue Jul  1 03:35:21 PM +03 2025 - MODIFY on /home/lan/snap/steam/common/.local/share/Steam/logs/controller.txt
Tue Jul  1 03:35:21 PM +03 2025 - MODIFY on /home/lan/snap/steam/common/.local/share/Steam/logs/console_log.txt
Tue Jul  1 03:35:21 PM +03 2025 - MODIFY on /home/lan/snap/steam/common/.local/share/Steam/logs/remote_connections.txt
```

Not much on saving in these logs either

```sh
cat $(find /home/lan/snap/steam/common/.local/share/Steam/logs/) | grep 'save'

# output
[...]
[2025-07-01 14:36:43] Roaming store 'userroaming' saved successfully
[...]
```

2025-07-01 Wk 27 Tue - 15:46

I found that there is a `Sandfall` directory in the game directory itself:

`~/parti2/Steam/steamapps/common/Expedition\ 33/Sandfall/`

It seems mostly binary data. We can still search for any mention of saving in 

```sh
search_term="EXPEDITION"
in_path="/home/lan/parti2/Steam/steamapps/common/Expedition 33/Sandfall/"

function grep_text_or_bin() {
	search_term="$1"
	in_path="$2"
	
	find "$in_path" -type f | while read -r file; do
	  if file "$file" | grep -q text; then
	    # Text file, grep directly
		echo TEXT $file
	    grep --color=always -H "$search_term" "$file"
	  else
	    # Binary file, search inside strings output
		echo BIN $file
	    strings "$file" | grep --color=always --label="$file" -H "$search_term"
	  fi
	done
}

grep_text_or_bin "$search_term" "$in_path"
```

(For this script's starting template, see [[001 General Assist Archive#1.1 Grep via strings for binary or text|LLM Query]].)

```sh
search_term="save"
in_path="/home/lan/parti2/Steam/steamapps/common/Expedition 33/Sandfall/"
grep_text_or_bin "$search_term" "$in_path" | less -R
```

Of note,

```
/home/lan/parti2/Steam/steamapps/common/Expedition 33/Sandfall/Binaries/Win64/SandFall-Win64-Shipping.exe:cannot open savepoint - SQL statements in progress
/home/lan/parti2/Steam/steamapps/common/Expedition 33/Sandfall/Binaries/Win64/SandFall-Win64-Shipping.exe:no such savepoint: %s
/home/lan/parti2/Steam/steamapps/common/Expedition 33/Sandfall/Binaries/Win64/SandFall-Win64-Shipping.exe:cannot release savepoint - SQL statements in progress
```

This file also has strings like `SaveToFile`

2025-07-01 Wk 27 Tue - 16:24

Let's try to trigger on file modifications in `~/parti2/Steam/steamapps/common/Expedition\ 33/Sandfall/`:

```sh
in_path="/home/lan/parti2/Steam/steamapps/common/Expedition 33/Sandfall/"

inotifywait -m -r -e modify -e create -e delete \
  "$in_path" |
  while read path action file; do
    echo "$(/bin/date) - $action on $path$file"
  done
```

No changes occur in there while going back to the game or saving.

```sh
in_path="/home/lan/parti2/Steam/steamapps/common/Expedition 33/"

inotifywait -m -r -e modify -e create -e delete \
  "$in_path" |
  while read path action file; do
    echo "$(/bin/date) - $action on $path$file"
  done
```

This doesn't show anything either. It doesn't seem these files are changed.

There is an in-game string we're interested in: `Save in progess...`

```sh
search_term="Save"
in_path="/home/lan/parti2/Steam/steamapps/common/Expedition 33/"

function grep_text_or_bin() {
	search_term="$1"
	in_path="$2"
	
	find "$in_path" -type f | while read -r file; do
	  if file "$file" | grep -q text; then
	    # Text file, grep directly
		echo TEXT $file
	    rg "$search_term" "$file"
	  else
	    # Binary file, search inside strings output
		echo BIN $file
	    strings "$file" | rg "$search_term"
	  fi
	done
}

grep_text_or_bin "$search_term" "$in_path" | less -R
```

For some reason this seems to hang... The file `/home/lan/parti2/Steam/steamapps/common/Expedition 33/Sandfall/Content/Paks/pakchunk999-Windows.ucas` is big.

It has strings like

```
truGFrameBindingOverlayOverlaySlofaulCidgetTree/G/UI/s/HUD_Exploration/SaveMenu/
```

### 3.3.1 Looking for where game file changes happen

So far we looked at the game directory itself, watched file changes in the local steam logs (`/home/lan/snap/steam/common/.local/share/Steam/`), and tried to correlate changes with actions in-game that triggers the in-game string `Save in progess...`

We still need to know exactly where in-game changes trigger a response to files.

One thing we haven't tried is quitting the game, and see if this triggers any relevant changes to saving.

#### 3.3.1.1 Trying to watch on everything in compatdata/<game_id>/pfx/

This directory is rather deep, so we will what happens...

```sh
in_path="/home/lan/snap/steam/common/.local/share/Steam/steamapps/compatdata/1903340/pfx/"

inotifywait -m -r -e modify -e create -e delete \
  "$in_path" |
  while read path action file; do
    echo "$(/bin/date) - $action on $path$file"
  done
```

Nothing. Even though we can touch and delete files ourselves and that be detected in various places there:

```
Tue Jul  1 04:53:56 PM +03 2025 - CREATE on /home/lan/snap/steam/common/.local/share/Steam/steamapps/compatdata/1903340/pfx/a
Tue Jul  1 04:54:00 PM +03 2025 - DELETE on /home/lan/snap/steam/common/.local/share/Steam/steamapps/compatdata/1903340/pfx/a
Tue Jul  1 04:54:18 PM +03 2025 - CREATE on /home/lan/snap/steam/common/.local/share/Steam/steamapps/compatdata/1903340/pfx/drive_c/users/steamuser/Downloads/a
Tue Jul  1 04:54:25 PM +03 2025 - DELETE on /home/lan/snap/steam/common/.local/share/Steam/steamapps/compatdata/1903340/pfx/drive_c/users/steamuser/Downloads/a
```

Nothing happens even on game restart.

### 3.3.2 Searching for all `*.sav` files on my system

```sh
find / -type f -name "*.sav" 2>/dev/null | less
```
^script-search-sys-for-sav

It's in 

```
/media/lan/parti2/Steam/steamapps/compatdata/1903340/pfx/drive_c/users/steamuser/AppData/Local/Sandfall/Saved/SaveGames/76561198101410413/EXPEDITION_0.sav
```

### 3.3.3 Watching on new compatdata path

2025-07-01 Wk 27 Tue - 17:15

The objective is done. But it could be fun to see if any interesting files change here on save.

```sh
in_path="/media/lan/parti2/Steam/steamapps/compatdata/1903340/pfx/"

inotifywait -m -r -e modify -e create -e delete \
  "$in_path" |
  while read path action file; do
    echo "$(/bin/date) - $action on $path$file"
  done
```

They are!

When touching the flag:

| Status | File                      |
| ------ | ------------------------- |
| MODIFY | EXPEDITION_0.sav          |
| MODIFY | EXPEDITION_0.sav          |
| MODIFY | PlatformSaveData.sav      |
| MODIFY | PlatformSaveData.sav      |
| MODIFY | Backup/SavesContainer.sav |
| MODIFY | Backup/SavesContainer.sav |
| MODIFY | SavesContainer.sav        |
| MODIFY | SavesContainer.sav        |

When leaving the flag:

| Status | File                                      |
| ------ | ----------------------------------------- |
| MODIFY | EXPEDITION_0.sav                          |
| MODIFY | EXPEDITION_0.sav                          |
| MODIFY | PlatformSaveData.sav                      |
| MODIFY | PlatformSaveData.sav                      |
| CREATE | Backup/EXPEDITION_0_2025_7_1_14_25_0.sav  |
| MODIFY | Backup/EXPEDITION_0_2025_7_1_14_25_0.sav  |
| MODIFY | Backup/EXPEDITION_0_2025_7_1_14_25_0.sav  |
| DELETE | Backup/EXPEDITION_0_2025_6_20_5_47_50.sav |
| MODIFY | Backup/SavesContainer.sav                 |
| MODIFY | Backup/SavesContainer.sav                 |
| MODIFY | SavesContainer.sav                        |
| MODIFY | SavesContainer.sav                        |

Seems to have a queue where it adds a new back up and deletes the oldest each time.


## 3.4 Creating correspondences with others

Since I was able to resolve this for me, let's link this technical journal over there.

- [ ] Corresponds with to [post on clair obscur expedition 33 save location](<https://primagames.com/tips/clair-obscur-expedition-33-save-file-location>) [[#^2]]

Seems I'm unable to sign up to leave a comment... Let's just email them: [feedback@primagames.com](mailto://feedback@primagames.com)


# 4 References

1. [steam forum: on save data](<https://steamcommunity.com/discussions/forum/1/1643170269579547670/>) ^1
2. [post on clair obscur expedition 33 save location](<https://primagames.com/tips/clair-obscur-expedition-33-save-file-location>) ^2