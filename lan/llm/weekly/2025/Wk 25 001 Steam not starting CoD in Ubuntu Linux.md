This relates to issue [001 Steam not starting CoD in Ubuntu Linux](../../../topics/steam/issues/001%20Steam%20not%20starting%20CoD%20in%20Ubuntu%20Linux.md).

Refer to [Wk 25 000 Steam says Game started but nothing happens](Wk%2025%20000%20Steam%20says%20Game%20started%20but%20nothing%20happens.md) for some techniques used here.

Instructions for LLM:

* This is a diagnostic document and not a conversation. Everything shared is context. Address the questions tagged (Q#) like (Q1) for example. If you see something like (~1), assume it part of the archive and not a latest set of questions.
  * Since it keeps occurring, I ask Again
  * !!! NEVER RESPOND TO (~1), (~2), etc.
  * ONLY respond to the tagged questions. Nothing else.

<hr class="__chatgpt_plugin">

### 0.1.1 role::user

When starting CoD on forced compatibility mode in steam, it just quickly reverts back to green button "Play"

<hr class="__chatgpt_plugin">

### 0.1.2 role::user

### 0.1.3 Let's first discover what its Steam ID is

[Previously](Wk%2025%20000%20Steam%20says%20Game%20started%20but%20nothing%20happens.md#clair-obscur-desktop1) we found a `*.desktop` file with the ID. Here is how we find all of them:

````sh
find -L /home/lan/snap/steam/common/.local/share/Steam/steamapps/compatdata/*/pfx/dosdevices/z:/proc/*/cwd/.local/share/applications/ -maxdepth 1 -type f 2>/dev/null
````

````sh
$ cat /home/lan/snap/steam/common/.local/share/Steam/steamapps/compatdata/1262350/pfx/dosdevices/z:/proc/2629062/cwd/.local/share/applications/Call\ of\ Duty.desktop 
[Desktop Entry]
Name=Call of Duty®
Comment=Play this game on Steam
Exec=snap run steam steam://rungameid/1938090
Icon=steam_icon_1938090
Terminal=false
Type=Application
Categories=Game;

````

OK. We know we are ID 1938090 for CoD.

2025-07-01 Wk 27 Tue - 14:10

Also see [Finding the ID via the steam store page](../../../topics/steam/tasks/000%20Copy%20save%20data%20from%20Clair%20Obscur%20Expedition%2033.md#21-finding-the-id-via-the-steam-store-page).

### 0.1.4 Launching the game via the terminal via proton

Just Launch:

````sh
STEAM_COMPAT_DATA_PATH="/home/lan/snap/steam/common/.local/share/Steam/steamapps/compatdata/1938090" STEAM_COMPAT_CLIENT_INSTALL_PATH="/home/lan/snap/steam/common/.local/share/Steam" '/home/lan/snap/steam/common/.local/share/Steam/steamapps/common/Proton Hotfix'/proton waitforexitandrun ~/parti2/Steam/steamapps/common/Call\ of\ Duty\ HQ/cod.exe
````

<a name="launch1" />^launch1

Debugging:

````sh
WINEDEBUG=+loaddll,+seh,+pid,+tid,+module STEAM_COMPAT_DATA_PATH="/home/lan/snap/steam/common/.local/share/Steam/steamapps/compatdata/1938090" STEAM_COMPAT_CLIENT_INSTALL_PATH="/home/lan/snap/steam/common/.local/share/Steam" '/home/lan/snap/steam/common/.local/share/Steam/steamapps/common/Proton Hotfix'/proton waitforexitandrun ~/parti2/Steam/steamapps/common/Call\ of\ Duty\ HQ/cod.exe
````

<a name="launch2" />^launch2

Via Steam:

````sh
steam -applaunch 1938090
````

<a name="launch3" />^launch3

#### 0.1.4.1 Errors

[Just launching](Wk%2025%20001%20Steam%20not%20starting%20CoD%20in%20Ubuntu%20Linux.md#launch1) produces the following errors:

````
Traceback (most recent call last):
  File "/home/lan/snap/steam/common/.local/share/Steam/steamapps/common/Proton Hotfix/proton", line 1869, in <module>
    g_session.init_session(sys.argv[1] != "runinprefix")
    ~~~~~~~~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/home/lan/snap/steam/common/.local/share/Steam/steamapps/common/Proton Hotfix/proton", line 1776, in init_session
    g_compatdata.setup_prefix()
    ~~~~~~~~~~~~~~~~~~~~~~~~~^^
  File "/home/lan/snap/steam/common/.local/share/Steam/steamapps/common/Proton Hotfix/proton", line 914, in setup_prefix
    with self.prefix_lock:
         ^^^^^^^^^^^^^^^^
  File "/home/lan/snap/steam/common/.local/share/Steam/steamapps/common/Proton Hotfix/filelock.py", line 323, in __enter__
    self.acquire()
    ~~~~~~~~~~~~^^
  File "/home/lan/snap/steam/common/.local/share/Steam/steamapps/common/Proton Hotfix/filelock.py", line 271, in acquire
    self._acquire()
    ~~~~~~~~~~~~~^^
  File "/home/lan/snap/steam/common/.local/share/Steam/steamapps/common/Proton Hotfix/filelock.py", line 384, in _acquire
    fd = os.open(self._lock_file, open_mode)
FileNotFoundError: [Errno 2] No such file or directory: '/home/lan/snap/steam/common/.local/share/Steam/steamapps/compatdata/1938090/pfx.lock'
````

2025-06-18 Wk 25 Wed - 09:52

Let's check `protondb`. This one was for  [Clair Obscur: Expedition 33](https://www.protondb.com/app/1903340) and this one is for [CoD](https://www.protondb.com/app/1938090)

![Pasted image 20250618095452.png](../../../../attachments/Pasted%20image%2020250618095452.png)

There they report that it's "BORKED" and unsupported.

##### 0.1.4.1.1 On the comment there by Spoon "Unsupported Anti-Cheat (Ricochet Anti-Cheat)"

They flag Kernel-Level Anti-Cheat as the issue. Labeling it "Ricochet Anti-Cheat". They say this has become standard since the 2019 reboot of the MW series and it uses elements exclusive to the windows kernel. They said the developer mentioned if someone finds a workaround they won't be punished so long as they do not interfere with the anti-cheat's functions.
