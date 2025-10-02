---
status: pend
---

# 1 Objective

For some games where it is currently difficult to get working with wine/proton.

# 2 Journal

## 2.1 Trying emugaming docker

2025-07-19 Wk 29 Sat - 18:15

[gh emugaming](https://github.com/lg/emugaming) [<a name="1" />^1](003%20Run%20Windows%20games%20on%20Docker%20or%20VM%20in%20Linux%20if%20wine%20fails.md#1).

````sh
mkdir -p ~/src/cloned/gh/lg
cd ~/src/cloned/gh/lg/
git clone git@github.com:lg/emugaming.git
cd emugaming
````

````sh
DOCKER_BUILDKIT=1 docker build -t emugaming .
````

2025-07-19 Wk 29 Sat - 18:33

Hmm. I can't run it right away, seems to freeze. Also retrieval of ISO seems manual and not clear where it should go.

Let's try this fork.

[gh emengweb/emu-windows](https://github.com/emengweb/emu-windows).

````
mkdir -p ~/src/cloned/gh/lg/forked/emengweb
cd ~/src/cloned/gh/lg/forked/emengweb
git clone git@github.com:emengweb/emu-windows.git
````

[docker compose issue](003%20Run%20Windows%20games%20on%20Docker%20or%20VM%20in%20Linux%20if%20wine%20fails.md#31-cant-create-bind-file-on-docker-compose-up).

### 2.1.1 Getting ISOs

2025-07-19 Wk 29 Sat - 21:03

In the README instructions of [gh emengweb/emu-windows](https://github.com/emengweb/emu-windows),

Download [Microsoft Windows 10 ISO](https://www.microsoft.com/en-us/software-download/windows10ISO) and locate it as `iso/win10.iso`.

Download [virtio-win.iso](https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/stable-virtio/virtio-win.iso) and locate it as `iso/virtio.iso`.

## 2.2 Trying quick-vm

[gh quick-vm](https://github.com/thecmdrunner/quick-vm).

2025-07-19 Wk 29 Sat - 22:35

It needs the same ISOs in [2.1.1 Getting ISOs](003%20Run%20Windows%20games%20on%20Docker%20or%20VM%20in%20Linux%20if%20wine%20fails.md#211-getting-isos).

Let's put them in `/var/lib/libvirt/images/` as `win.iso` and `virtio-win.iso`

# 3 Tasks

## 3.1 Formatting scripts (skipped)

2025-07-19 Wk 29 Sat - 20:20

For fork [gh emengweb/emu-windows](https://github.com/emengweb/emu-windows),

Tried to use formatter `shell-format`, but it's not recognized. Tried to use `Bash Beautify`, and it works, but it removes tabs from multiline commands which is not good...

# 4 Issues

## 4.1 can't create bind file on docker compose up and qemu file error

* [ ] 

### 4.1.1 Solution

````sh
apt-get install qemu-system
````

````
qemu-img create -f qcow2  disk.qcow2 20G
````

PEND

### 4.1.2 Journal

2025-07-19 Wk 29 Sat - 19:03

For fork [gh emengweb/emu-windows](https://github.com/emengweb/emu-windows),

````
emu-windows-1  | /init.sh: line 9: can't create /sys/class/vtconsole/vtcon0/bind: Read-only file system
emu-windows-1  | qemu-system-x86_64: -hda /disk.qcow2: 'file' driver requires '/disk.qcow2' to be a regular file
emu-windows-1 exited with code 0
````

2025-07-19 Wk 29 Sat - 20:34

LLM assistance [here](../../../../../llm/weekly/latest/Wk%2029%20001%20-%20Resolving%20Qemu%20and%20Docker%20issues%20for%20Windows%20Docker%20Build.md).

2025-07-19 Wk 29 Sat - 19:06

Maybe I'm missing some instructions?

I don't have `qemu-img`...

[qemu docs](https://www.qemu.org/docs/master/), [qemu download](https://www.qemu.org/download/).

````sh
apt-get install qemu-system
````

2025-07-19 Wk 29 Sat - 19:48

It doesn't seem like my changes to `init.sh` are registering even though I build the image again with `./build.sh`.

Hmm. Seems to work fine now... I am testing the script with

````sh
./build.sh && docker compose up && docker compose down
````

Now I'm getting

````
emu-windows-1  | qemu-system-x86_64: -soundhw: invalid option
````

They explain in this [post](https://www.reddit.com/r/termux/comments/18k5iw9/comment/ke0st0r/?utm_source=share&utm_medium=web3x&utm_name=web3xcss&utm_term=1&utm_content=share_button) that soundhw was deprecated for audiodev.

[docs](https://www.qemu.org/docs/master/system/qemu-manpage.html) have some usage example for it:

````
-audiodev alsa,id=example,in.frequency=44110,out.frequency=8000
-audiodev alsa,id=example,out.channels=1 # leaves in.channels unspecified
-audiodev pa,id=pa
````

Using `-audodev pa,id=pa`.

Okay we're back to the error

````
emu-windows-1  | qemu-system-x86_64: -hda /disk.qcow2: 'file' driver requires '/disk.qcow2' to be a regular file
````

[docs](https://www.qemu.org/docs/master/system/qemu-manpage.html) explain,

 > 
 > Older options like `-hda` are essentially macros which expand into `-drive` options for various drive interfaces. The original forms bake in a lot of assumptions from the days when QEMU was emulating a legacy PC, they are not recommended for modern configurations.

````
emu-windows-1  | qemu-system-x86_64: -drive /disk.qcow2: warning: short-form boolean option '/disk.qcow2' deprecated
emu-windows-1  | Please use /disk.qcow2=on instead
````

2025-07-19 Wk 29 Sat - 20:54

Let's modify `win7.qcow2` and rename it to just `disk.qcow2` in `docker-compose.yml`. It otherwise creates a folder when it shouldn't. We should also create the file itself:

````
qemu-img create -f qcow2  disk.qcow2 20G
````

From the commented out code in init.sh

````sh
#qemu-system-x86_64 -smp 2 -m 4G -drive if=none,id=disk0,cache=none,format=qcow2,aio=threads,file=/disk.qcow2 -vnc 0.0.0.0:0 -cdrom /iso/win7.iso  &
````

We can see that `/iso` is meant to be a directory, it also uses a newer option rather than `-hda`, but `-drive` with the arguments

````sh
-drive if=none,id=disk0,cache=none,format=qcow2,aio=threads,file=/disk.qcow2
````

We need to use `-cdrom` for the win10 ISO at least. But there's also another ISO...

2025-07-19 Wk 29 Sat - 21:32

For now disabling sound to see if things work without.

http://localhost:6901/ displays `RFB 003.008`

This is because it's not meant to be viewed in the web. It can be opened via Remmina for example.

It says

````
boot failed: could not read thee boot disk
[...]
No bootable device
````

Let's try to add win10 as a cdrom and try again.

2025-07-19 Wk 29 Sat - 21:49

Now it's installing, though this has to be followed manually.

Also with this being over VNC, I am not sure how fast it can be for gaming.

2025-07-19 Wk 29 Sat - 21:57

Installation doesn't progress. It can't find a drive.

When eabling virtio.iso I get

````
emu-windows-1  | qemu-system-x86_64: -cdrom /iso/win10.iso: drive with bus=1, unit=0 (index=2) exists
````

I thought this indicated that I have to choose either, but I need to be more specific with the options:

````
  -drive file=/disk.qcow2,if=ide,format=qcow2 \
  -drive file=/iso/win10.iso,media=cdrom,index=2 \
  -drive file=/iso/virtio.iso,media=cdrom,index=3 \
````

It is also  able to recognize the 20GB drive now, so installing.

## 4.2 Obsolete version in docker compose

2025-07-19 Wk 29 Sat - 19:24

For fork [gh emengweb/emu-windows](https://github.com/emengweb/emu-windows),

````
WARN[0000] /home/lan/src/cloned/gh/lg/forked/emengweb/emu-windows/docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion 
````

Just gonna remove that from `docker-compose.yml`

## 4.3 No pulseaudio on system but needed by qemu-system

2025-07-19 Wk 29 Sat - 21:25

We are using

````
-audodev pa,id=pa
````

To install pulseaudio,

````sh
sudo apt install pulseaudio
````

To test...

````sh
pulseaudio

# out
W: [pulseaudio] pid.c: Stale PID file, overwriting.
E: [pulseaudio] socket-server.c: bind(): Address already in use
E: [pulseaudio] module.c: Failed to load module "module-native-protocol-unix" (argument: ""): initialization failed.
E: [pulseaudio] main.c: D-Bus name org.pulseaudio.Server already taken.
````

Hmm. We have pipewire though. We need to look into sound issues more here.

# 5 Ideas

## 5.1 PR to add Docker Example

2025-07-19 Wk 29 Sat - 18:43

This [PR](https://github.com/SuMaiKaDe/blinko-telegram-bot/pull/3) is interesting contribution from [emengweb](https://github.com/emengweb), basically adding docker support to projects that still don't have it.

# 6 Resources

1. [gh emugaming](https://github.com/lg/emugaming) ^1
