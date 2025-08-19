---
status: done
---
2025-08-18 Wk 34 Mon - 21:25

# 1 Objective

I want to be able to get a Youtube song downloaded on my PC, and then copied to my phone via remote SSH access and finally accessible as a song in the `Music` app in LineageOS.

# 2 Journal


2025-08-18 Wk 34 Mon - 10:59

Previously, we [[000 Install open source mobile OS on my phone|installed LineageOS]] as an open source mobile operating system alternative to Andoid. We also rooted the device with Magisk and gained superuser and [[000 Install open source mobile OS on my phone#3.3 Getting SSH remote access to my Pixel 3|SSH access]] to the device.

Now we need to put new songs in `/sdcard/Music`. 

We can just scp files over with Termux, then su and magisk and move things there.

To download a song from youtube, we use `yt-dlp` and `ffmpeg,

```sh
python3 -m pip install yt-dlp
sudo apt-get install ffmpeg
```

Download the video as `*.webm` then convert it to mp3

```sh
yt-dlp {song_url}
ffmpeg -i {song}.webm -q:a 0 -map a {song}.mp3
```

Activate `sshd` in Termux.

If you don't know your phone's ip address, you can for example find it with

```
# in phone Termux App
su
ip addr | grep 192
```

Copy the song over

```
scp -P 8022 {song}.mp3 {mobile_user}@{mobile_ip}:/sdcard/Music
```

Now from the Music app (source: [Twelve](https://github.com/BlueN1nja1/Improved-Twelve/)), Activate Settings -> Trigger a MediaStore database rescan. And you should have the song!

2025-08-18 Wk 34 Mon - 12:04

For an automation script,

```sh
#!/bin/sh

mobile_user="$1"
mobile_ip="$2"
song_url="$3"

filename_downloaded="$(yt-dlp ${song_url} 2>&1 | grep "Merger" | cut -d'"' -f2)" || exit 1
basename="$(basename -s .webm "$filename_downloaded")" || exit 1

ffmpeg -i "$filename_downloaded" -q:a 0 -map a "$basename.mp3" || exit 1

scp -P 8022 "$basename.mp3" $mobile_user@$mobile_ip:/sdcard/Music || exit 1

rm "$filename_downloaded"

# Uncomment if you don't want to keep the mp3 on your PC
# rm "$basename.mp3"
```

Save this as `download_yt_song_to_phone.sh` and do `chmod +x download_yt_song_to_phone.sh`. 

Then you can just download a song directly to your phone:

```
./download_yt_song_to_phone.sh {mobile_user} {mobile_ip} {song_url}
```

Remember unless you have the `sshd` service enabled, it will have to be activated on your phone.

You may also need to activate the scan setting in your Music app.

And now you have a new song on your phone, easy!

## 2.1 Issues encountered

2025-08-18 Wk 34 Mon - 11:27

After copying file to Download and then deleting it, was not able to copy it over there again because 

Copying over to a `*.webm` song to `/storage/emulated/0/Music` does not make it appear in the list.

Spawn [[#6.1 Where does LineageOS Music store and load music?]] ^spawn-invst-45b932

2025-08-18 Wk 34 Mon - 11:52

Spawn [[#5.1 Convert webm to mp3 in shell]] ^spawn-howto-117dd5

2025-08-18 Wk 34 Mon - 11:58

We weren't able to copy directly

```
scp -P 8022 {song}.mp3 {mobile_user}@{mobile_ip}:/storage/emulated/0/Music
```

But we can do so over `/sdcard/Music`.

# 3 Tasks

# 4 Issues

# 5 HowTos

## 5.1 Convert webm to mp3 in shell

- [x] 

From [[#^spawn-howto-117dd5]] in [[#2.1 Issues encountered]]

2025-08-18 Wk 34 Mon - 11:53

From [unixmen tutorial](https://www.unixmen.com/webm-to-mp3-how-can-you-convert-in-linux/),

```
ffmpeg -i WEBMFileName.webm -q:a 0 -map a MP3FileOutput.mp3
```

# 6 Investigations

## 6.1 Where does LineageOS Music store and load music?

- [x] 

### 6.1.1 Solution

We should be able to copy `.mp3` files to `/sdcard/Music` (alternatively, `/storage/emulated/0/Music`).

### 6.1.2 Journal

From [[#^spawn-invst-45b932]] in [[#2.1 Issues encountered]]

[Eleven](https://github.com/dwi336/Eleven), [BlueN1nja1/Eleven](https://github.com/BlueN1nja1/Eleven?tab=readme-ov-file) (discontinued) -> [BlueN1nja1/Improved-Twelve](https://github.com/BlueN1nja1/Improved-Twelve)

Mentions [Magisk] and rooting in [Special Versions](https://github.com/BlueN1nja1/Improved-Twelve?tab=readme-ov-file#special-versions).

2025-08-18 Wk 34 Mon - 11:41

I created a playlist `TEST NAME FIND ME` so that we can try to locate where files are stored

```
ssh {mobile_user}@{mobile_ip} -p8022

# in Phone SSH Session
su
find / 2>/dev/null | grep "TEST NAME FIND ME"

# out
[nothing]
```

2025-08-18 Wk 34 Mon - 11:48

I was able to add a test file and find that Twelve *does* respond to `/storage/emulated/0/Music` (files also appear under `/sdcard/Music` learned about in this [reddit post](https://www.reddit.com/r/LineageOS/comments/18l1lh1/comment/kdv88f3/?utm_source=share&utm_medium=web3x&utm_name=web3xcss&utm_term=1&utm_content=share_button)).

```
# in Phone SSH Session
touch /storage/emulated/0/Music/a.mp3
```

I've been trying to put a `*.webm` file here. And I also touched an `*.mp4`. But those are video formats and it doesn't register them in.


# 7 Ideas

# 8 Side Notes
# 9 External Links

# 10 References