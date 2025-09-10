---
status: done
---


#lan #setup #gaming #unresolved

# 1 Objective

- [x] Install Osu on Ubuntu 25.04

# 2 Journal

2025-07-03 Wk 27 Thu - 00:25


Following [github: Installing PipreWire](https://github.com/NelloKudo/osu-winello/wiki/Installing-PipeWire) [[#^2]], 

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

- [x] 

2025-07-29 Wk 31 Tue - 23:14

There are guides like [xp-pen blog for playing osu with tablet](https://www.xp-pen.com/blog/how-to-play-osu-with-tablet-exact-guide.html), but not sure much is needed besides just connecting a tablet? 

 I just have the [ugee m708 tablet](https://www.ugee.com/drawing-tablets/m708).

2025-07-29 Wk 31 Tue - 23:40

It's mostly responsive but it seems like the pointer gets stuck sometimes for a second or so? I am able to win an easy song at least, but not sure why I'm failing on normal when I can sortt of track.

Can also use Z or X to press and just the stylus to track also.

## 3.2 Delete song templates downloaded

- [x] 

2025-07-29 Wk 31 Tue - 22:28

In [[#4.1 Downloaded songs cannot be played]] we learned that we can download templates from artists, but those are not mapped and cannot be played and are made for editing. Let's delete those.

I could just delete them from the osu-wine app, but I'd like to know where they are stored.

This [osu forum post](https://osu.ppy.sh/community/forums/topics/961228?n=3) gives details on where they would be stored on Windows.


In [NelloKudo/osu-winello/wiki/Moving-skins-songs-from-Windows](https://github.com/NelloKudo/osu-winello/wiki/Moving-skins-songs-from-Windows),

they recommend `osu-wine --info`.

```sh
osu-wine --info

# out
[...]
    Wineprefix location: /home/lan/.local/share/wineprefixes/osu-wineprefix
    osu! folder: '/home/lan/.local/share/osu-wine/osu!'
[...]
```


So we can go delete the files there directly or back them up:

```sh
nautilus /home/lan/.local/share/osu-wine/osu!
```

Folder backed up under `/home/lan/src/cloned/winlenovo/backups/2025-Wk31-000-osu-songs`.

# 4 Issues

## 4.1 Downloaded songs cannot be played

- [x] 

2025-07-29 Wk 31 Tue - 21:59

So in `Play`, it says "Beatmap could not be loaded successfully".

So from the artist [ZVLIAN](https://osu.ppy.sh/beatmaps/artists/455) for example, if I hover over download for "DEPTH OF FIELD"  it says "download beatmap template"

So I guess this is to make it easy to map, but it's not actually mapped?

By their name you can click "osu! Beatmaps" and find that others featured them as the artist and mapped some of their work. I guess they are templates.

Here is one mapped [song](https://osu.ppy.sh/beatmapsets/2240082#osu/4760807).

2025-07-29 Wk 31 Tue - 22:19

Downloading that works, so that should solve the mystery.

Strangely there are songs like [this](https://osu.ppy.sh/beatmapsets/2325198#taiko/4983955) which feature the artist but are not listed under [the artist search](https://osu.ppy.sh/beatmapsets?q=artist%3D%22%22ZVLIAN%22%22)...

It says "Latest pending version" and doesn't show a rank scoreboard in osu, is this related as to why?


# 5 Ideas

## 5.1 Open source contribution

2025-07-03 Wk 27 Thu - 00:59

- [ ] Clarify pactl installation in README for osu [[#^doc-issue1]]
	- [ ]  Open Issue on this
# 6 References
1. [github: Installing Osu](<https://github.com/NelloKudo/osu-winello?tab=readme-ov-file>) ^1
2. [github: Installing PipreWire](<https://github.com/NelloKudo/osu-winello/wiki/Installing-PipeWire>) ^2