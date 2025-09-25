# 1 Steam says Game started but nothing happens

This relates to [000 Steam says Game started but nothing shows](../../../topics/steam/issues/000%20Steam%20says%20Game%20started%20but%20nothing%20shows.md).

Instructions:

* Everything shared is context. Address the questions in tagged (Q#) like (Q1) for example. If you see something like (~1), assume it part of the archive and not a latest set of questions.
  * Since it keeps occurring, I ask Again
  * !!! NEVER RESPOND TO (~1), (~2), etc.
  * ONLY respond to the tagged questions. Nothing else.

<hr class="__chatgpt_plugin">

### 1.1.1 role::user

How can I see diagnostics for this? When I press "Start" and it just says "X STOP". Can I view logs? Can I start a game as if I started it from steam, except through the terminal?

Note that I am running this game with compatibility mode forced on:

Proton Hotfix.

<hr class="__chatgpt_plugin">

### 1.1.2 role::assistant<span style="font-size: small;"> (chatgpt-4o)</span>

Distilled,

#### 1.1.2.1 To enable Proton logs

* [ ] Confirmed
  Add these to launcher options:

(Latest)

(Attempt 1)

````sh
export PROTON_LOG=1
export PROTON_LOG_DIR="$HOME/proton_logs"
export PROTON_DEBUG=1 # optionally for more info
````

Clarification:

* How do I add this in Steam to Properties>General>Launch Options?

(Attempt 2)

````sh
PROTON_LOG=1 PROTON_LOG_DIR="$HOME/proton_logs" %command%
# OR
PROTON_LOG=1 PROTON_DEBUG=1 PROTON_LOG_DIR="$HOME/proton_logs" %command%
````

Clarification:

* (~2) No `$HOME/proton_logs` files get created when I start the game. Maybe proton is not even reached, or this is false.

(Attempt 3)

LATER

#### 1.1.2.2 To find the game's ID via "SteamDB"?

* [x] Confirmed

(Attempt 1)

````sh
cd ~/.steam/steam/steamapps
grep -Ri "Your Game Name" appmanifest_*.acf
````

Errors:

````sh
$ cd ~/.steam/
cd: no such file or directory: /home/lan/.steam/
````

(Attempt 2)

````sh
cd ~/.local/share/Steam/steamapps
grep -Ri "Your Game Name" appmanifest_*.acf
````

Errors:

````sh
$ cd ~/.local/share/Steam          
cd: no such file or directory: /home/lan/.local/share/Steam
````

(~1) Can you not give me one path after another? List every possible path so I try them all or how to find the paths on my system.

(Attempt 3)

Find the appmanifest files:

````sh
$ find $HOME -type f -name "appmanifest_*.acf" 2>/dev/null
/home/lan/snap/steam/common/.local/share/Steam/steamapps/
````

I was able to find this file:

````sh
$ cat ~/snap/steam/common/.local/share/Steam/steamapps/compatdata/2180100/pfx/dosdevices/z:/proc/2629043/task/2629094/cwd/.local/share/applications/Clair\ Obscur\ Expedition\ 33.desktop

[Desktop Entry]
Name=Clair Obscur: Expedition 33
Comment=Play this game on Steam
Exec=snap run steam steam://rungameid/1903340
Icon=steam_icon_1903340
Terminal=false
Type=Application
Categories=Game;
````

<a name="clair-obscur-desktop1" />^clair-obscur-desktop1

#### 1.1.2.3 To launch from the terminal

* [ ] Confirmed

````sh
steam -applaunch <AppID>
````

To see if 1903340 is what I'm looking for, I try

````sh
$ steam -applaunch 1903340
Testing for explicit PulseAudio choice...
...and PulseAudio has been explicitly chosen, so using it.
INFO: filtering /home/lan/.local/share/flatpak/exports/share out of XDG_DATA_DIRS because it is unreachable
INFO: filtering /var/lib/flatpak/exports/share out of XDG_DATA_DIRS because it is unreachable

Found NVIDIA version: 570.133.07
Need NVIDIA 32-bit: False
````

I confirm that in steam, "PLAY" turns into "X STOP" when I run this in the terminal.

#### 1.1.2.4 To find out what  process is spawned and continues running

(Attempt 1)

(~1) How do I use strace to just show me what is spawned including pids? I know something is still around that "X STOP" remains there. And I wanna know what.

I tried

````sh
strace -e trace=execve steam -applaunch 1903340
````

but it's not obvious.

(Attempt 2)

LLM suggests

* Use of pstree? but on pid, I wanna launch `steam -applaunch myself`
  * (~1) How do you make this work with the earlier command? I don't want to attach anywhere.
* Also suggested `ps -ef --forest | grep steam`

Also this:

````
strace -f -e execve -s 256 steam -applaunch 1903340
````

Seems when we add `-f` we get to see the PIDs!

But there is a problem. It will not launch the game:

````
cannot fstatat canonical snap directory: Permission denied
+++ exited with 1 +++
````

(~2) I need to be able to trace what process is started. Ideally I really just want to see logs from it. It does seem to print things to the same stdout, but it's not much.

#### 1.1.2.5 Using pstree to see what is spawned

Via LLM Suggestion

````sh
steam -applaunch 1903340 &; pid=$! && echo pid is $pid && sleep 3 && echo PSTREE && pstree -p $pid 
````

(~1) pstree here just outputs nothing. Why?

````sh
ps -ef --forest | grep -E 'steam|proton|Expedition' | grep -v grep
````

This outputs too much. I killed all the processes with "Expedition" in them, there must have been like 8.

I can confirm now on launch of

````sh
steam -applaunch 1903340
````

The first one seems to take into account the launcher options I added earlier

````sh
$ ps -ef | grep Expedition
bin/sh -c PROTON_LOG=1 PROTON_LOG_DIR="$HOME/proton_logs" /home/lan/snap/steam/common/.local/share/Steam/ubuntu12_32/steam-launch-wrapper -- /home/lan/snap/steam/common/.local/share/Steam/ubuntu12_32/reaper SteamLaunch AppId=1903340 -- '/home/lan/snap/steam/common/.local/share/Steam/steamapps/common/SteamLinuxRuntime_sniper'/_v2-entry-point --verb=waitforexitandrun -- '/home/lan/snap/steam/common/.local/share/Steam/steamapps/common/Proton Hotfix'/proton waitforexitandrun  '{parti2}/Steam/steamapps/common/Expedition 33/Expedition33_Steam.exe'
````

(~1) We know these are all running. But I do not see any further logs in stdout. Is it because steam itself was not launched through the terminal? Is this likely just the process hanging silently?

LLM Suggests

(Option 1)

````
journalctl --user-unit=steam -f
snap logs steam
````

(~1) None of these show anything.

(Option 2)
Debugging into the currently running process

````sh
strace -p <pid> -s 256 -f
gdb -p <pid> # then use bt or info threads or continue
````

(~2) Gives Error:

````
$ strace -p 3103469 -s 256 -f
strace: attach: ptrace(PTRACE_SEIZE, 3103469): Operation not permitted
$ gdb -p 3103469
ptrace: Operation not permitted.
````

LLM Suggests "seccomp/AppArmor" is behind all these issues. Snap sandbock security protections.

Fine, I don't need to do this through snap, but I do not want to figure out the proton environment variables/anything either. I need to at least be able to see what proton command the game would have launched.

From what I recorded earlier we can sort of see:

````sh
'/home/lan/snap/steam/common/.local/share/Steam/steamapps/common/Proton Hotfix'/proton waitforexitandrun  '{parti2}/Steam/steamapps/common/Expedition 33/Expedition33_Steam.exe'
````

But this is insufficient:

````
Proton: No compat data path?
````

I need to not speculate but capture these details.

(~1) Is this possible? And if so how?

LLM suggests security issues will make this difficult.

#### 1.1.2.6 Getting all logs from Snap Steam

[this steam forum answer](https://steamcommunity.com/discussions/forum/1/135509758529538311/#c135509758529700366) suggests that logs are generally found in steam root. or under /logs/... Answer is for windows but let's check.

(~1) Create a command that just watches for file changes in a path. Whenever a file changes, it logs a line. I want to trace for *log changes* in `/home/lan/snap/steam/common/.local/share/Steam/`

````sh
$ sudo apt install inotify-tools

$ inotifywait -m -r -e modify -e create -e delete \
  /home/lan/snap/steam/common/.local/share/Steam/ |
  while read path action file; do
    echo "$(/bin/date) - $action on $path$file"
  done
````

(~1) I can tell it's responsive, but for some reason it says

````
zsh: command not found: date
````

even though I can run `echo "$(date)"` just fine in zsh over terminal:

````sh
$ echo "$(date)"
Tue Jun 17 03:11:43 PM +03 2025
````

Suggests $(date) -> $(/bin/date). That works.

Although a lot of logs are showing, the last one was

````
/home/lan/snap/steam/common/.local/share/Steam/logs/appinfo_log.txt
````

Let's examine that.

Nothing bad there. Let's try to find out what happens now when we launch the app.

There are so many DELETEs... Let's just focus on CREATE and MODIFY... and then MODIFY if CREATE is too much, but I imagine it may recreate logs so I wanna show it.

(~1) Modify the command to also output to `~/inotifywait.log` in addition to stdout.

````sh
rm ~/inotifywait.log && inotifywait -m -r -e modify -e create \
  /home/lan/snap/steam/common/.local/share/Steam/ |
  while read path action file; do
    echo "$(/bin/date) - $action on $path$file"
  done | tee -a ~/inotifywait.log
````

Then launch the game and examine what logs are written

````sh
cat ~/inotifywait.log | grep --color=always log | less -E
````

(~1) Why does `less -E` just exit on G? I want it to say "You reached the end of the pager" or whatever it usually says on other systems. Beeps at you in the end.

I ran a funny command

````sh
$ less --help | less
````

Searching for `/\-E` I get

````
  -e  -E  ....  --quit-at-eof  --QUIT-AT-EOF
````

I thought this was for colors. I think that's `-R`:

````
  -r  -R  ....  --raw-control-chars  --RAW-CONTROL-CHARS
````

Anyway, some interesting notes:

````sh
$ cat /home/lan/snap/steam/common/.local/share/Steam/logs/compat_log.txt | less

[2025-06-17 15:43:15] Command prefix for tool 2180100 "Proton Hotfix" set to: "'/home/lan/snap/steam/common/.local/share/Steam/steamapps/common/SteamLinuxRuntime_sniper'/_v2-entry-point --verb=run -- '/home/lan/snap/steam/common/.local/share/Steam/steamapps/common/Proton Hotfix'/proton run ".

$ cat /home/lan/snap/steam/common/.local/share/Steam/logs/console-linux.txt | less

[2025-06-17 15:43:14] ERROR: ld.so: object '/home/lan/snap/steam/common/.local/share/Steam/ubuntu12_32/gameoverlayrenderer.so' from LD_PRELOAD cannot be preloaded (wrong ELF class: ELFCLASS32): ignored.

$ 
````

(~1) What is up with that ld.so error?

LLM suggests this is a harmless error....

So we have a time correlate "2025-06-16 15:43" and all logs are of the same format. Let's grep for "error" in `/home/lan/snap/steam/common/.local/share/Steam/`

````sh
rg "2025-06-17 15:43" /home/lan/snap/steam/common/.local/share/Steam/ | rg -i "error"
````

We can also see all logs at once

````sh
rg "2025-06-17 15:43" /home/lan/snap/steam/common/.local/share/Steam/ --color=always | less -R
````

This only shows errors about ld.so

(~1) What is this SteamLinuxRuntime_sniper and why is it so significant? Also why am I not able to find any important logs anywhere here. No errors. This is based on all the logs I've seen modified in the Steam root. This seems like a design issue at this point. I should have been able to grep on some critical errors if there were any at this point.

Edit

* Do not repeat my priors back to me. I don't want to hear strace mentioned again or anything snap security.

LLM Suggests it's like a container

````
Steam -> SteamLinuxRuntime_sniper (sets up sandbox) -> Proton -> Windows Game
````

(~1) Is this gonna be useful to emulate or invoke directly? If so how?

(~2) Even if proton never starts to show me its logs I need to at least see an error on it NOT starting. Why not? Could steam be writing errors to somewhere other than the steam root?

#### 1.1.2.7 Using strace to find pids

Still on this fork.  `strace -f` is not able to get us to the point steam launches because of root permissions. and if you do sudo, then it will reject running as root.

(~2) How to move forward specifically with this strace strategy?

LLM Suggests this is a snap permissions wall, and snap will not allow this further.

#### 1.1.2.8 Trying to launch proton through SteamLinuxRuntime_sniper

2025-06-17 Wk 25 Tue - 16:46

````sh
$ /home/lan/snap/steam/common/.local/share/Steam/steamapps/common/SteamLinuxRuntime_sniper/_v2-entry-point --verb=run -- /bin/sh  

pressure-vessel-wrap[3745678]: W: Using libGLX_mesa.so.0 from provider system for some but not all architectures! Will take /usr/share/drirc.d from provider.
````

(~1) Error

We know it runs applications within but just didn't give me a persistent shell:

````sh
$ home/lan/snap/steam/common/.local/share/Steam/steamapps/common/SteamLinuxRuntime_sniper/_v2-entry-point --verb=run -- /bin/echo "hello from inside sniper"

pressure-vessel-wrap[3758088]: W: Using libGLX_mesa.so.0 from provider system for some but not all architectures! Will take /usr/share/drirc.d from provider.
hello from inside sniper
````

The compat path issue is still persistent even within this environment:

````sh
$ home/lan/snap/steam/common/.local/share/Steam/steamapps/common/SteamLinuxRuntime_sniper/_v2-entry-point --verb=run -- '/home/lan/snap/steam/common/.local/share/Steam/steamapps/common/Proton Hotfix'/proton waitforexitandrun  '{parti2}/Steam/steamapps/common/Expedition 33/Expedition33_Steam.exe'

pressure-vessel-wrap[3767452]: W: Using libGLX_mesa.so.0 from provider system for some but not all architectures! Will take /usr/share/drirc.d from provider.
Proton: No compat data path?
````

(~1) How do I find out how this is launched such that it resolved the compat data path issue?

Check logs again for this on  a new time correlate

````sh
rg "2025-06-17 16:59" /home/lan/snap/steam/common/.local/share/Steam/ --color=always | less -R
````

Can't find anything related to `STEAM_COMPAT_DATA_PATH`

(~2) What should be passed so it works anyway?

````sh
export STEAM_COMPAT_DATA_PATH="/home/lan/snap/steam/common/.local/share/Steam/steamapps/compatdata/1903340"
export STEAM_COMPAT_CLIENT_INSTALL_PATH="/home/lan/snap/steam/common/.local/share/Steam"
````

(~1) Why does this silently exits?

Edit:

* Do not say the env variables are set incorrectly. If you remove one or the other you will get a responsive error. compat path or the steam compat client install path. My command as it is makes those disappear, so it is not wrong.

````sh
/home/lan/snap/steam/common/.local/share/Steam/steamapps/common/SteamLinuxRuntime_sniper/_v2-entry-point --verb=run -- STEAM_COMPAT_DATA_PATH="/home/lan/snap/steam/common/.local/share/Steam/steamapps/compatdata/1903340" '/home/lan/snap/steam/common/.local/share/Steam/steamapps/common/Proton Hotfix'/proton waitforexitandrun  '{parti2}/Steam/steamapps/common/Expedition 33/Expedition33_Steam.exe'

pressure-vessel-wrap[3806900]: W: Using libGLX_mesa.so.0 from provider system for some but not all architectures! Will take /usr/share/drirc.d from provider.
````

To simplify, let us run this ourselves outside the SteamLinuxRuntime_sniper

````sh
STEAM_COMPAT_DATA_PATH="/home/lan/snap/steam/common/.local/share/Steam/steamapps/compatdata/1903340" '/home/lan/snap/steam/common/.local/share/Steam/steamapps/common/Proton Hotfix'/proton waitforexitandrun  '{parti2}/Steam/steamapps/common/Expedition 33/Expedition33_Steam.exe'
````

Ok it wants the other environmental variable

````
steamdir = os.environ["STEAM_COMPAT_CLIENT_INSTALL_PATH"]
               ~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
````

````sh
STEAM_COMPAT_DATA_PATH="/home/lan/snap/steam/common/.local/share/Steam/steamapps/compatdata/1903340" STEAM_COMPAT_CLIENT_INSTALL_PATH="/home/lan/snap/steam/common/.local/share/Steam" '/home/lan/snap/steam/common/.local/share/Steam/steamapps/common/Proton Hotfix'/proton waitforexitandrun  '{parti2}/Steam/steamapps/common/Expedition 33/Expedition33_Steam.exe'
````

This too silently exits. Gives different outputs.

First invocation,

````
Proton: Upgrading prefix from None to 10.0-200 (/home/lan/snap/steam/common/.local/share/Steam/steamapps/compatdata/1903340/)
fsync: up and running.
````

Afterwards,

````
fsync: up and running.
````

Enable even more debugging!

````sh
WINEDEBUG=+loaddll,+seh,+pid,+tid,+module STEAM_COMPAT_DATA_PATH="/home/lan/snap/steam/common/.local/share/Steam/steamapps/compatdata/1903340" STEAM_COMPAT_CLIENT_INSTALL_PATH="/home/lan/snap/steam/common/.local/share/Steam" '/home/lan/snap/steam/common/.local/share/Steam/steamapps/common/Proton Hotfix'/proton waitforexitandrun  '{parti2}/Steam/steamapps/common/Expedition 33/Expedition33_Steam.exe'
````

Some errors I find

````
0120:0124:err:steamclient:steamclient_init unable to load native steamclient library                                                                                                          
0120:0124:err:msvcrt:_wassert (L"!status",L"../src-lsteamclient/steamclient_main.c",375)

0120:0124:warn:module:import_dll No implementation for ntdll.dll.Error imported from L"C:\\Program Files (x86)\\Steam\\steamclient64.dll", setting to 00000000014111B8                        

0120:0124:warn:module:import_dll No implementation for ntdll.dll.?V_UnicodeRepair@@YAHPEADW4EStringConvertErrorPolicy@@@Z imported from L"C:\\Program Files (x86)\\Steam\\steamclient64.dll", 
````

(~2) Are there other other debugging stuff I can add like +loaddll ? Where do you find the list of all the options ofr WINEDEBUG? ([link](https://gitlab.winehq.org/wine/wine/-/wikis/Debug-Channels))

Just log everything with +all... Yeah it's not very happy with that.

````
trace:virtual:virtual_init host addr space limit: 0x7fffffff0000
wine: error opening ftrace file: Permission denied.
````

LLM Suggested issues with vulkan. It is true I cannot find any logs on vulkan whatsoever. usually when I start the game over steam it says it loads the vulkan shaders.

Just from +seh we can see some details:

````
0134:warn:seh:dispatch_exception "[S_API] SteamAPI_Init(): Loaded 'C:\\Program Files (x86)\\Steam\\steamclient64.dll' OK.\n"
0134:err:steamclient:steamclient_init unable to load native steamclient library
0134:trace:seh:raise (22)
````

2025-06-17 Wk 25 Tue - 17:40

LLM keeps assuming the environmental variables are set wrong for some reason. You can launch apps like notepad.exe or cmd.exe

````sh
STEAM_COMPAT_DATA_PATH="/home/lan/snap/steam/common/.local/share/Steam/steamapps/compatdata/1903340" STEAM_COMPAT_CLIENT_INSTALL_PATH="/home/lan/snap/steam/common/.local/share/Steam" '/home/lan/snap/steam/common/.local/share/Steam/steamapps/common/Proton Hotfix'/proton waitforexitandrun cmd
````

and in cmd,

````sh
set STEAM_COMPAT_CLIENT_INSTALL_PATH
STEAM_COMPAT_CLIENT_INSTALL_PATH="/home/lan/snap/steam/common/.local/share/Steam" '/home/lan/snap/steam/common/.local/share/Steam/steamapps/common/Proton 
````

But in the same *spawning shell*

````sh
$ echo $STEAM_COMPAT_CLIENT_INSTALL_PATH
[nothing]
````

This should be proof there is no such issue.

Windows does not have use for this. It's just the Proton launcher that needs it.

#### 1.1.2.9 Reset avenues prior to reboot

2025-06-17 Wk 25 Tue - 17:58

No more looking for logs or causes for this problem. This is all about resets.

(~1) First time, steam says it's loading vulkan shaders. What can I reset without rebooting that triggers this again? Unload all shaders. Reset all GPU stuff.

````sh
rm -rf ~/snap/steam/common/.cache/*
````

(~2) Is there anything else we can explore to reset? anything before a full hard PC reset.

#### 1.1.2.10 Post reboot running the app via proton via the terminal

All that remains is a reboot of my PC. And to see if the command to run the game just works then from the terminal.

(~1) Do the logs presented show plausible cause for the silent exit? Or is it more plausibly an error on part of Clair Obscur the game itself? Or perhaps a silent error to do with Vulcan shader environment? Who is responsible for this?

<hr class="__chatgpt_plugin">

### 1.1.3 role::user

I was actually able to launch the game by killing all processes with the word "windows" in them.
