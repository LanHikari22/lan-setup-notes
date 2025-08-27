---
status: pend
---

# 1 Objective

Given a screencast video, just count the number of turns passed.

# 2 Journal

# 3 Tasks

## 3.1 Create Script to press keys at exact intervals

* [x] 

2025-08-27 Wk 35 Wed - 18:10

We want to measure the range of error on parries. But this cannot be done with much human input variance. We also do not get any clear signs from the game to anchor us visually for this. So instead, we will test this with scripts.

Related: [Wk 27 001 On visual input, press some keys](../../entries/weekly/2025/Wk%2027%20001%20On%20visual%20input,%20press%20some%20keys.md)

2025-08-27 Wk 35 Wed - 18:17

We can extract from `/home/lan/src/cloned/gh/LanHikari22/lan-exp-scripts/scripts/2025/weekly/Wk27-000-expedition-33-ui-auto/golgra-skip-dialog.py`.

We used `yodotool` to press keys. This had visual input also, but we do not need this.

Here's what we need:

````python
import subprocess
import os

YTOOL = "/home/lan/src/cloned/gh/ReimuNotMoe/ydotool/build/ydotool"
SOCKET = "/home/lan/.ydotool_socket"

def ydotool(*args):
    subprocess.run(
        [YTOOL, *args], env={**os.environ, "YDOTOOL_SOCKET": SOCKET}, check=True
    )
	
# E Key
ydotool("key", "18:1")
ydotool("key", "18:0")

def beep(which=0):
    if which == 0:
        subprocess.run(["aplay", "/usr/share/sounds/sound-icons/glass-water-1.wav"])
````

````python
import time

time.sleep(SOME_TIME_MS)
````

2025-08-27 Wk 35 Wed - 18:26

We also need to be able to listen for keys, and execute our script on them.

Spawn [5.1 Listen for key events in Linux in Python3](000%20Create%20script%20to%20analyze%20expedition%2033%20golgra%20fight%20and%20count%20turns.md#51-listen-for-key-events-in-linux-in-python3) <a name="spawn-howto-ba135a" />^spawn-howto-ba135a

2025-08-27 Wk 35 Wed - 20:20

We need to run ydotool:

````sh
cd ~/src/cloned/gh/ReimuNotMoe/ydotool/build
./ydotoold --socket-path="$HOME/.ydotool_socket"
````

2025-08-27 Wk 35 Wed - 20:41

You can find the script in [lan-exp-scripts Wk35-000-expedition-33-parry-range-test](https://github.com/LanHikari22/lan-exp-scripts/tree/main/scripts/2025/weekly/Wk35-000-expedition-33-parry-range-test).

For valid key codes, see [keycodes](https://pickpj.github.io/keycodes.html).

2025-08-27 Wk 35 Wed - 21:12

This should be usable now! It could also be modified to allow for monitoring of when parry and ghost keys have been pressed. Studying that could give us some insight into our variance.

# 4 Issues

# 5 HowTos

## 5.1 Listen for key events in Linux in Python3

* [x] 

From [^spawn-howto-ba135a](000%20Create%20script%20to%20analyze%20expedition%2033%20golgra%20fight%20and%20count%20turns.md#spawn-howto-ba135a) in [3.1 Create Script to press keys at exact intervals](000%20Create%20script%20to%20analyze%20expedition%2033%20golgra%20fight%20and%20count%20turns.md#31-create-script-to-press-keys-at-exact-intervals)

2025-08-27 Wk 35 Wed - 18:30

First let's see if we can do this with yodotool

2025-08-27 Wk 35 Wed - 18:35

No, [gh ydotool](https://github.com/ReimuNotMoe/ydotool) shows it's only for ouputting key events.

[stackoverflow post](https://stackoverflow.com/questions/1409216/receiving-key-press-and-key-release-events-in-linux-terminal-applications) -> {[linuxjournal The linux keyboard driver](https://www.linuxjournal.com/article/1080)}

In this [askubuntu post](https://askubuntu.com/questions/1197651/ubuntu-show-what-keys-are-pressed-in-real-time) they recommend `xev` but it spawns an application and only detects keys within it

2025-08-27 Wk 35 Wed - 18:43

Hmm. We know that [keyd](https://github.com/rvaiya/keyd) was able to get notifications...

But no way to get route that to an application it seems.

2025-08-27 Wk 35 Wed - 18:59

(LLM ChatGPT)

LLM suggested `pynput` on python3, but that gives an error on Wayland that it requires an X server. `ImportError: this platform is not supported: ('failed to acquire X connection ...`

LLM suggested using `evdev`.

Checking the devices,

````python
from evdev import InputDevice, categorize, ecodes, list_devices

# List all input devices
devices = [InputDevice(path) for path in list_devices()]
for dev in devices:
    print(dev.path, dev.name)

# out (relevant)
/dev/input/event[N] keyd virtual pointer
/dev/input/event[M] keyd virtual keyboard
````

Then we can use this to be notified of key events:

````python
from evdev import InputDevice, categorize, ecodes, list_devices

dev = InputDevice('/dev/input/event{N}')

print(f"Listening to {dev.name}...")

for event in dev.read_loop():
    if event.type == ecodes.EV_KEY:
        key_event = categorize(event)
        if key_event.keystate == key_event.key_down:
            print(f"Key pressed: {key_event.keycode}")

````

(/LLM ChatGPT)

2025-08-27 Wk 35 Wed - 19:39

Here's a full functional script that can do some action on specific keys pressed:

````python
import os
from typing import List
from evdev import InputDevice, categorize, ecodes

INPUT_DEV = os.environ['INPUT_DEV']

def listen_on_key_events_pressed(keycodes: List[str], callback):
    dev = InputDevice(INPUT_DEV)
    print(f"Listening to {dev.name}...")

    for event in dev.read_loop():
        if event.type == ecodes.EV_KEY:
            key_event = categorize(event)
            if key_event.keystate == key_event.key_down:
                if len(keycodes) == 0:
                    print(f"Key pressed: {key_event.keycode}")
                if key_event.keycode in keycodes:
                    callback()

def run():
    print('Hello!')


if __name__ == '__main__':
    listen_on_key_events_pressed(['KEY_KP6', 'KEY_2'], run)
````

# 6 Investigations

# 7 Ideas

# 8 Side Notes

# 9 External Links

# 10 References
