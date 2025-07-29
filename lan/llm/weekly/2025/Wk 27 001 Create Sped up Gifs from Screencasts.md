
# 1 Objective

This relates to [[Wk 26 005 Linux screen recording to gif]]. There, we learned to turn a screen cast into a gif. Now I have an 18-minute screencast I want to become a 2 minute gif.


# 2 LLM Instructions
- This is a diagnostic document and not a conversation. Everything shared is context. Address the questions tagged (Q#) like (Q1) for example. If you see something like (~1), assume it part of the archive and not a latest set of questions.
	- Since it keeps occurring, I ask Again
	- !!! NEVER RESPOND TO (~1), (~2), etc.
	- ONLY respond to the tagged questions. Nothing else.

(LLM chatgpt-4o)
(Note: Between these tags is responses from ChatGPT-4o.
(/LLM chatgpt-4o)

(LLM chatgpt-4o)
(/LLM chatgpt-4o)

# 3 Journal

2025-07-02 Wk 27 Wed - 17:39

[[Wk 26 005 Linux screen recording to gif#^src-solution|Before]], we used

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


Now we want to use `ffmpeg` to instead compress the video to a specified length instead of stripping ends. 

(LLM chatgpt-4o)

```sh
input_webm="/home/lan/Videos/Screencasts/Screencast From 2025-07-02 16-49-00.webm"
output_gif="$(echo $(echo $input_webm | rev | cut -f 2- -d '.' | rev).gif)"

# 18 min = 1080 s → 2 min = 120 s ⇒ speed-up factor ≈ 9
ffmpeg -i "$input_webm" \
  -vf "setpts=PTS/9,fps=10,scale=800:-1:flags=lanczos" \
  -an output.gif
```

(/LLM chatgpt-4o)

(Q 17:42) Objective

# 4 HowTos

## 4.1 Stripping just the extension from the filename

2025-07-02 Wk 27 Wed - 17:56

[Answer](<https://stackoverflow.com/a/32584935/6944447>) by [Manish Singh](https://stackoverflow.com/users/518493/manish-singh) on stripping extensions,

```sh
echo $filename | rev | cut -f 2- -d '.' | rev
```



# 5 References
