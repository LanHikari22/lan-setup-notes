#lan #llm #linux #sh #gif #resolved 

# 1 Linux Screen recording to gif

# 2 Objective

Peek alternative. It would be best if I can start it from the terminal to record the entire screen, or optionally a pid or string for the application name.

- [x] Able to quickly record the screen and generate a gif
- [x] Has a clear route for termination like via Ctrl+C or some hotkey
- [x] Works on Wayland

# 3 LLM Instructions
- This is a diagnostic document and not a conversation. Everything shared is context. Address the questions tagged (Q#) like (Q1) for example. If you see something like (~1), assume it part of the archive and not a latest set of questions.
	- Since it keeps occurring, I ask Again
	- !!! NEVER RESPOND TO (~1), (~2), etc.
	- ONLY respond to the tagged questions. Nothing else.

(LLM chatgpt-4o)
(Note: Between these tags is responses from ChatGPT-4o.
(/LLM chatgpt-4o)

(LLM chatgpt-4o)
(/LLM chatgpt-4o)

# 4 Solution

2025-06-26 Wk 26 Thu - 19:03

We will be using GNOME/wayland's built-in screen-casting via the PrintScr button:

1. Press PrintScr
2. Switch from "Take Screenshot" to "Record Screen"
3. Press Capture
4.  When done, outside full screen, there will be a red recording status in the status bar. Press stop (white square)

Now we can open the webm generated and see what range of times we're interested in capturing to gif. 

We will use the following script:

```sh
#!/bin/bash

# Usage: ./webm2gif.sh <webm_path> <start_time> <stop_time>
# Example: ./webm2gif.sh ~/Videos/cast-20250626-173727.webm 00:00:05 00:00:15

in="$1"
start="$2"
stop="$3"
gifout="${in%.webm}.gif"

if [[ -z "$in" || -z "$start" || -z "$stop" ]]; then
  echo "Usage: $0 <webm_path> <start_time> <stop_time>"
  exit 1
fi

# Convert HH:MM:SS to seconds
hms_to_sec() {
  IFS=: read -r h m s <<< "$1"
  echo "$((10#$h * 3600 + 10#$m * 60 + 10#$s))"
}

start_sec=$(hms_to_sec "$start")
stop_sec=$(hms_to_sec "$stop")
duration=$((stop_sec - start_sec))

if (( duration <= 0 )); then
  echo "Invalid time range: stop time must be after start time"
  exit 1
fi

echo "Converting $in to $gifout (from $start to $stop, duration $duration sec)"
ffmpeg -i "$in" -ss "$start" -t "$duration" \
  -vf "fps=10,scale=800:-1:flags=lanczos" -c:v gif "$gifout"

echo "Saved ➜ $gifout"
```


It can be used like this:

```sh
~/src/exp/scripts/gif-record/webm2gif.sh ~/Videos/Screencasts/Screencast\ From\ 2025-06-26\ 18-46-58.webm 00:00:07 00:00:25

# output
Saved ➜ /home/lan/Videos/Screencasts/Screencast From 2025-06-26 18-46-58.gif
```

The script can also be added to `$PATH`.

# 5 Journal

## 5.1 Tool suggestion

### 5.1.1 Attempt 1

2025-06-26 Wk 26 Thu - 17:30

(LLM chatgpt-4o)
The tool:
```sh
sudo apt install byzanz xdotool
```

Start recording:
```sh
gifout="$HOME/Videos/cast-$(date +%Y%m%d-%H%M%S).gif"
echo "Recording … hit Ctrl-C when done"
byzanz-record --cursor --delay=0  "$gifout"   # stops when you Ctrl-C
echo "Saved  ➜  $gifout"
```
(/LLM chatgpt-4o)

(~1) This will not work. Solution must be for Wayland. Error:

```
Recording … hit Ctrl-C when done
Given area is not inside desktop.
Saved  ➜  /home/lan/Videos/cast-20250626-173727.gif
```

### 5.1.2 Attempt 2

2025-06-26 Wk 26 Thu - 17:41


(LLM chatgpt-4o)
The tool:
```sh
sudo apt install wf-recorder ffmpeg
```

Start recording:
```sh
outfile="/tmp/out-$(date +%s).mp4"
gifout="$HOME/Videos/cast-$(date +%Y%m%d-%H%M%S).gif"
echo "Recording… Press Ctrl+C to stop"
wf-recorder -f "$outfile"
echo "Converting to GIF…"
ffmpeg -i "$outfile" -vf "fps=10,scale=800:-1:flags=lanczos" -c:v gif "$gifout"
echo "Saved ➜ $gifout"
```
(/LLM chatgpt-4o)

(~1) Still an error for `wf-recorder`

```
Recording… Press Ctrl+C to stop
compositor doesn't support wlr-screencopy-unstable-v1
```

### 5.1.3 Attempt 3

`gnome-screencast` via `DBus` + `ffmpeg` -> `GIF`


Start recording:
```sh
out="$(xdg-user-dir VIDEOS)/cast-$(date +%Y%m%d-%H%M%S).webm"
gdbus call --session \
  --dest org.gnome.Shell.Screencast \
  --object-path /org/gnome/Shell/Screencast \
  --method org.gnome.Shell.Screencast.Screencast \
  "$out" "\[\]"
```

(~2) Error:

```
zsh: no matches found: []
```

Seems `zsh` needs to quote escape `[]`s.

(~2) Error: This causes the GUI notification `"Screencast ended unexpectedly"`

Save:
```sh
gifout="${out%.webm}.gif"
ffmpeg -i "$out" -vf "fps=10,scale=800:-1:flags=lanczos" -c:v gif "$gifout"
echo "Saved ➜ $gifout"

```

### 5.1.4 Attempt 4
[screen-recorders-wayland blog](<https://www.debugpoint.com/screen-recorders-linux-wayland/>) [[#^2]] mentions that the gnome screenshot tool already has video recording. I didn't notice, this could be used. It's gonna be more convenient than peek's weird UI interfering with the apps and having strange layouts.

(Q1) This can work. Now I just have to turn it to a gif, with the options to strip it between two times HH:MM:SS.


(LLM chatgpt-4o)
```sh
#!/bin/bash

# Usage: ./webm2gif.sh <webm_path> <start_time> <stop_time>
# Example: ./webm2gif.sh ~/Videos/cast-20250626-173727.webm 00:00:05 00:00:15

in="$1"
start="$2"
stop="$3"
gifout="${in%.webm}.gif"

if [[ -z "$in" || -z "$start" || -z "$stop" ]]; then
  echo "Usage: $0 <webm_path> <start_time> <stop_time>"
  exit 1
fi

# Convert HH:MM:SS to seconds
hms_to_sec() {
  IFS=: read -r h m s <<< "$1"
  echo "$((10#$h * 3600 + 10#$m * 60 + 10#$s))"
}

start_sec=$(hms_to_sec "$start")
stop_sec=$(hms_to_sec "$stop")
duration=$((stop_sec - start_sec))

if (( duration <= 0 )); then
  echo "Invalid time range: stop time must be after start time"
  exit 1
fi

echo "Converting $in to $gifout (from $start to $stop, duration $duration sec)"
ffmpeg -i "$in" -ss "$start" -t "$duration" \
  -vf "fps=10,scale=800:-1:flags=lanczos" -c:v gif "$gifout"

echo "Saved ➜ $gifout"

```
(/LLM chatgpt-4o)

Just had to make sure that `-i "$in"` came first, or it would error.

Now we can record webm then just get a gif at the timestamps we would like:

```sh
~/src/exp/scripts/gif-record/webm2gif.sh ~/Videos/Screencasts/Screencast\ From\ 2025-06-26\ 18-46-58.webm 00:00:07 00:00:25

# output
Saved ➜ /home/lan/Videos/Screencasts/Screencast From 2025-06-26 18-46-58.gif
```

# 6 References
1. [wf-recorder tut](<https://commandmasters.com/commands/wf-recorder-linux/>) ^1
2. [screen-recorders-wayland blog](<https://www.debugpoint.com/screen-recorders-linux-wayland/>) ^2