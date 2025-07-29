---
resolved: "false"
status: todo
---
#lan #setup #gaming #unresolved

# 1 Objective

- [x] Install Osu on Ubuntu 25.04

# 2 Journal

2025-07-03 Wk 27 Thu - 00:25


Following [github: Installing PipreWire](<https://github.com/NelloKudo/osu-winello/wiki/Installing-PipeWire>) [[#^2]], 

We get an error:

```sh
systemctl --user --now disable pulseaudio.service pulseaudio.socket

# error
Failed to stop pulseaudio.socket: Unit pulseaudio.socket not loaded.
```

Although we are able to run this:

```sh
systemctl --user --now enable pipewire pipewire-pulse
```

But in [github: Installing Osu](<https://github.com/NelloKudo/osu-winello?tab=readme-ov-file>) [[#^1]], 

They recommend to check if `Server Name: Pulseaudio (on PipeWire)` exists via

```
export LANG=C
pactl info | grep "Server Name"
```

But I do not have this installed. ^doc-issue1

```sh
mkdir -p ~/src/cloned/gh/NelloKudo
cd ~/src/cloned/gh/NelloKudo
git clone https://github.com/NelloKudo/osu-winello.git
cd osu-winello
chmod +x ./osu-winello.sh
./osu-winello.sh
```

```sh
Winello: Where do you want to install the game?: 
          1 - Default path (/home/lan/.local/share/osu-wine)
		  2 - Custom path

# input
1
```

Then in a new terminal,

```sh
osu-wine
```

![[Pasted image 20250703005108.png]]


![[Pasted image 20250703005253.png]]

![[Pasted image 20250703005535.png]]

Killed all processes with `wine` and restarted and it seems to work. Though it may just have been that I couldn't find the window after minimizing.

2025-07-03 Wk 27 Thu - 00:58

But there is no sound.

Maybe because I am using bluetooth speakers, which is a bit odd. 

It works fine on headphones.

# 3 Tasks

## 3.1 Try to play songs using tablet

- [ ] 

### 3.1.1 Pend

# 4 Issues

## 4.1 Downloaded songs cannot be played

- [ ] 


### 4.1.1 Pend

# 5 Ideas

## 5.1 Open source contribution

2025-07-03 Wk 27 Thu - 00:59

- [ ] Clarify pactl installation in README for osu [[#^doc-issue1]]
	- [ ]  Open Issue on this
# 6 References
1. [github: Installing Osu](<https://github.com/NelloKudo/osu-winello?tab=readme-ov-file>) ^1
2. [github: Installing PipreWire](<https://github.com/NelloKudo/osu-winello/wiki/Installing-PipeWire>) ^2