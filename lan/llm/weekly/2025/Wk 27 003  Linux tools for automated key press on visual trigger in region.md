
# 1 Linux tools for automated key press on visual trigger in region

Related to [[Wk 27 001 On visual input, press some keys]]

# 2 Objective

- [x] To have a script that performs yodotool actions on visual cues silently
- [ ] To provide visual feedback for the user, such as a red boundary box on detection region or blue shade
# 3 Solution


# 4 LLM Instructions
- This is a diagnostic document and not a conversation. Everything shared is context. Address the questions tagged (Q#) like (Q1) for example. If you see something like (~1), assume it part of the archive and not a latest set of questions.
	- Since it keeps occurring, I ask Again
	- !!! NEVER RESPOND TO (~1), (~2), etc.
	- ONLY respond to the tagged questions. Nothing else.

(LLM chatgpt-4o)
(Note: Between these tags is responses from ChatGPT-4o.
(/LLM chatgpt-4o)

(LLM chatgpt-4o)
(/LLM chatgpt-4o)

# 5 Journal

(LLM chatgpt-4o)

For X11:
[xdotool](<https://github.com/jordansissel/xdotool>) (input) + [SikuliX](<https://github.com/RaiMan/SikuliX1>) (vision)

For Wayland:
[ydotool](<https://github.com/ReimuNotMoe/ydotool>) – a Wayland-safe replacement for xdotool

(/LLM chatgpt-4o)

For the script below you need:

 [[#^build1|build ydotool]].

See test script [here](<https://github.com/LanHikari22/lan-exp-scripts/blob/main/scripts/2025/Wk27-000-expedition-33-ui-auto/test.py>).

this scripts demonstrates proof of concept for basic reaction on visual cue.

## 5.1 Modifying the program to echo on detection of golgra chat

2025-07-05 Wk 27 Sat - 18:23

We will create a script that skips the losing Golgra dialog and gets us back to the fight. You can find it [here](<https://github.com/LanHikari22/lan-exp-scripts/blob/main/scripts/2025/Wk27-000-expedition-33-ui-auto/golgra-skip-dialog.py>)

2025-07-05 Wk 27 Sat - 15:02

Now that we have a working example of visual cue response, we can tailor it for our use case.

This will be our visual cue:

![[Pasted image 20250705152643.png]]

Saved to `golgra-as-expected-dialog.png` in `/home/lan/src/exp/scripts/2025-07-expedition-33-ui-auto`.

2025-07-05 Wk 27 Sat - 15:50

Had to add a 2 second delay for `cv2.imread` but otherwise we are able to detect the image.

```sh
cat /usr/include/linux/input-event-codes.h | grep "\bKEY_F\b"
cat /usr/include/linux/input-event-codes.h | grep "KEY_W\b"
cat /usr/include/linux/input-event-codes.h | grep "KEY_E\b"

# output
#define KEY_F                   33
#define KEY_W                   17
#define KEY_E                   18
```




# 6 Issues
## 6.1 ydotoold unavailable

### 6.1.1 Issue

```sh
ydotool mousemove -x -100 -y 100

# output
ydotool: notice: ydotoold backend unavailable (may have latency+delay issues)
```


### 6.1.2 Journal

2025-07-05 Wk 27 Sat - 00:11

```sh
sudo apt-get install ydotoold
```

2025-07-05 Wk 27 Sat - 00:16

This [ydotoold issue](<https://github.com/ReimuNotMoe/ydotool/issues/179>) and [stackexchange post](<https://unix.stackexchange.com/questions/745734/ydotool-first-backend-unavailable-then-crash-on-connection>) both point towards installation via apt being insufficient and it should be built from source instead. Let's uninstall.

```sh
sudo apt-get remove ydotool ydotoold
```

Following [build instructions](<https://github.com/ReimuNotMoe/ydotool?tab=readme-ov-file#build>), ^build1

```sh
mkdir -p ~/src/cloned/gh/ReimuNotMoe
cd ~/src/cloned/gh/ReimuNotMoe
git clone git@github.com:ReimuNotMoe/ydotool.git
cd ydotool
```

```sh
sudo apt-get install cmake
sudo apt install scdoc
```

```sh
mkdir build
cd build
cmake ..
make -j `nproc`
```

Then in two terminals,

```sh
cd ~/src/cloned/gh/ReimuNotMoe/ydotool/build
sudo ./ydotoold
```

```sh
cd ~/src/cloned/gh/ReimuNotMoe/ydotool/build
sudo ./ydotool mousemove -a -x 0 -y 0
```

2025-07-05 Wk 27 Sat - 00:40

Weird. Not sure if it's interaction with input leap or just because of this tool. But it seems to have logged me out of my session, after spamming "enter" in the terminal.

But otherwise it seems to work. Note that I had to `sudo` `ydotool` otherwise, I would get

```
failed to connect socket `/run/user/1000/.ydotool_socket': Connection refused
Please check if ydotoold is running.
```


## 6.2 Issue: Cannot import cv2

```sh
~/src/exp/scripts/2025-07-expedition-33-ui-auto/test.sh

# output
Traceback (most recent call last):
  File "/home/lan/src/exp/scripts/2025-07-expedition-33-ui-auto/test.sh", line 2, in <module>
    import os, time, subprocess, tempfile, cv2, numpy as np
ModuleNotFoundError: No module named 'cv2'
```

[This answer](<https://stackoverflow.com/questions/66387881/import-cv2-could-not-be-resolved>) suggests ^install-cv2

```sh
python3 -m pip install opencv-python opencv-python-headless
```

## 6.3 ydotool requires sudo


(LLM chatgpt-4o)

Put user in `input` group

```sh
sudo usermod -aG input "$USER"
```

Login and logout to apply group membership then check:

```sh
id -nG | grep -q '\binput\b' && echo "✓ you are in the input group"
```

Then run ydtoold:

```sh
cd ~/src/cloned/gh/ReimuNotMoe/ydotool/build
./ydotoold --socket-path="$HOME/.ydotool_socket"
export YDOTOOL_SOCKET="$HOME/.ydotool_socket"
```

Now we can use ydtool in user space:

```sh
export YDOTOOL_SOCKET="$HOME/.ydotool_socket"
./ydotool mousemove -a -x 0 -y 0
```


(/LLM chatgpt-4o)

## 6.4 Issues with [ydotool cv test script](<https://github.com/LanHikari22/lan-exp-scripts/blob/main/scripts/2025/Wk27-000-expedition-33-ui-auto/test.py>)

2025-07-05 Wk 27 Sat - 02:30

While testing for this, will need to remember to

```sh
rm ~/Pictures/Screenshot-*
```

Since it's creating many screenshots there.

2025-07-05 Wk 27 Sat - 01:09

Original,

```python
#!/usr/bin/env python3
import os, time, subprocess, tempfile, cv2, numpy as np

# ❶ grab a frame via the portal ---------------------------------
def screenshot_ppm():
    handle = subprocess.check_output([
        'dbus-send', '--session', '--print-reply',
        '--dest=org.freedesktop.portal.Desktop',
        '/org/freedesktop/portal/desktop',
        'org.freedesktop.portal.Screenshot.Screenshot',
        'boolean:false', 'a{sv}:'
    ]).decode()
    uri = handle.split('file://')[1].split("'")[0]
    return cv2.imread(uri)

# ❷ load reference once
tmpl = cv2.imread('retry_button.png', cv2.IMREAD_GRAYSCALE)
th, tw = tmpl.shape[::-1]

# ❸ event loop ---------------------------------------------------
while True:
    gray = cv2.cvtColor(screenshot_ppm(), cv2.COLOR_BGR2GRAY)
    res  = cv2.matchTemplate(gray, tmpl, cv2.TM_CCOEFF_NORMED)
    loc  = np.where(res >= 0.93)
    if loc[0].size:
        y, x = int(loc[0][0] + th/2), int(loc[1][0] + tw/2)
        subprocess.run(['ydotool', 'mousemove', str(x), str(y)])
        subprocess.run(['ydotool', 'click', '1'])
        time.sleep(0.4)                # small debounce
    time.sleep(0.1)
```

```sh
~/src/exp/scripts/2025-07-expedition-33-ui-auto/test.sh    

# output
[ WARN:0@0.009] global loadsave.cpp:268 findDecoder imread_('retry_button.png'): can't open/read file: check file path/integrity
Traceback (most recent call last):
  File "/home/lan/src/exp/scripts/2025-07-expedition-33-ui-auto/test.sh", line 18, in <module>
    th, tw = tmpl.shape[::-1]
             ^^^^^^^^^^
AttributeError: 'NoneType' object has no attribute 'shape'
```

Did not create a retry_button.png.

```sh
cd ~/src/exp/scripts/2025-07-expedition-33-ui-auto/
ls

# output
retry_button.png  test.sh
```

```sh
./test.sh

# output
dbus-send: Unknown type "a{sv}"
Traceback (most recent call last):
  File "/home/lan/src/exp/scripts/2025-07-expedition-33-ui-auto/./test.sh", line 22, in <module>
    gray = cv2.cvtColor(screenshot_ppm(), cv2.COLOR_BGR2GRAY)
                        ~~~~~~~~~~~~~~^^
  File "/home/lan/src/exp/scripts/2025-07-expedition-33-ui-auto/./test.sh", line 6, in screenshot_ppm
    handle = subprocess.check_output([
             ~~~~~~~~~~~~~~~~~~~~~~~^^
        'dbus-send', '--session', '--print-reply',
        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    ...<3 lines>...
        'boolean:false', 'a{sv}:'
        ^^^^^^^^^^^^^^^^^^^^^^^^^
    ]).decode()
    ^^
  File "/home/lan/miniconda3/lib/python3.13/subprocess.py", line 474, in check_output
    return run(*popenargs, stdout=PIPE, timeout=timeout, check=True,
           ~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
               **kwargs).stdout
               ^^^^^^^^^
  File "/home/lan/miniconda3/lib/python3.13/subprocess.py", line 579, in run
    raise CalledProcessError(retcode, process.args,
                             output=stdout, stderr=stderr)
subprocess.CalledProcessError: Command '['dbus-send', '--session', '--print-reply', '--dest=org.freedesktop.portal.Desktop', '/org/freedesktop/portal/desktop', 'org.freedesktop.portal.Screenshot.Screenshot', 'boolean:false', 'a{sv}:']' returned non-zero exit status 1.

```


Keep reference for diff,

```sh
cp test.sh test.0.sh
```

```diff
diff -u test.0.sh test.sh
--- test.0.sh   2025-07-05 01:17:32.383334957 +0300
+++ test.sh     2025-07-05 01:19:00.070733077 +0300
@@ -2,16 +2,28 @@
 import os, time, subprocess, tempfile, cv2, numpy as np
 
 # ❶ grab a frame via the portal ---------------------------------
+
 def screenshot_ppm():
-    handle = subprocess.check_output([
-        'dbus-send', '--session', '--print-reply',
-        '--dest=org.freedesktop.portal.Desktop',
-        '/org/freedesktop/portal/desktop',
-        'org.freedesktop.portal.Screenshot.Screenshot',
-        'boolean:false', 'a{sv}:'
-    ]).decode()
-    uri = handle.split('file://')[1].split("'")[0]
-    return cv2.imread(uri)
+    with tempfile.NamedTemporaryFile(suffix=".png", delete=False) as tmpfile:
+        tmp_path = tmpfile.name
+
+    # Trigger screenshot portal via gdbus
+    subprocess.run([
+        "gdbus", "call", "--session",
+        "--dest", "org.freedesktop.portal.Desktop",
+        "--object-path", "/org/freedesktop/portal/desktop",
+        "--method", "org.freedesktop.portal.Screenshot.Screenshot",
+        "''",  # app_id (empty string is okay)
+        "{'handle_token': <'golgra-auto-retry'>}",
+    ])
+
+    # Wait ~1s for portal to drop screenshot in ~/Pictures (it's slow)
+    import time, glob, os
+    time.sleep(1.0)
+    pics = sorted(glob.glob(os.path.expanduser("~/Pictures/Screenshot*.png")), key=os.path.getmtime)
+    if not pics:
+        raise RuntimeError("No screenshot found in ~/Pictures")
+    return cv2.imread(pics[-1])
```

2025-07-05 Wk 27 Sat - 01:20

```sh
./test.sh
Error: GDBus.Error:org.freedesktop.DBus.Error.NoReply: Message recipient disconnected from message bus without replying
Error: GDBus.Error:org.freedesktop.DBus.Error.NoReply: Message recipient disconnected from message bus without replying
```

For diff on last,

```diff
diff -u test.-1.sh test.sh
--- test.0.sh   2025-07-05 01:17:32.383334957 +0300
+++ test.sh     2025-07-05 01:28:25.875493788 +0300
@@ -1,17 +1,27 @@
 #!/usr/bin/env python3
-import os, time, subprocess, tempfile, cv2, numpy as np
+import os, time, subprocess, tempfile, cv2, glob, json, numpy as np
 
 # ❶ grab a frame via the portal ---------------------------------
+
 def screenshot_ppm():
-    handle = subprocess.check_output([
-        'dbus-send', '--session', '--print-reply',
-        '--dest=org.freedesktop.portal.Desktop',
-        '/org/freedesktop/portal/desktop',
-        'org.freedesktop.portal.Screenshot.Screenshot',
-        'boolean:false', 'a{sv}:'
-    ]).decode()
-    uri = handle.split('file://')[1].split("'")[0]
-    return cv2.imread(uri)
+    from pathlib import Path
+
+    # Request screenshot via portal
+    subprocess.run([
+        "gdbus", "call", "--session",
+        "--dest", "org.freedesktop.portal.Desktop",
+        "--object-path", "/org/freedesktop/portal/desktop",
+        "--method", "org.freedesktop.portal.Screenshot.Screenshot",
+        "''",  # app_id
+        '{}',  # empty options dict
+    ], check=True)
+
+    # Wait for portal to save it into ~/Pictures
+    time.sleep(1.0)
+    pics = sorted(Path.home().joinpath("Pictures").glob("Screenshot*.png"), key=os.path.getmtime)
+    if not pics:
+        raise RuntimeError("No screenshot found in ~/Pictures")
+    return cv2.imread(str(pics[-1]))

```

```sh
./test.sh
```

We are able to take screenshots now, although it's sort of annoying with the flash. Just gotta correct ydtool use.

Keep the final script as diff checkpoint:

```sh
cp test.sh test.-1.sh
```

```diff
diff -u test.-1.sh test.sh                                                                                                                             
--- test.-1.sh  2025-07-05 01:30:55.342379675 +0300
+++ test.sh     2025-07-05 01:36:37.534492904 +0300


@@ -23,18 +21,23 @@
         raise RuntimeError("No screenshot found in ~/Pictures")
     return cv2.imread(str(pics[-1]))
 
-# ❷ load reference once
-tmpl = cv2.imread('retry_button.png', cv2.IMREAD_GRAYSCALE)
-th, tw = tmpl.shape[::-1]
-
-# ❸ event loop ---------------------------------------------------
-while True:
-    gray = cv2.cvtColor(screenshot_ppm(), cv2.COLOR_BGR2GRAY)
-    res  = cv2.matchTemplate(gray, tmpl, cv2.TM_CCOEFF_NORMED)
-    loc  = np.where(res >= 0.93)
-    if loc[0].size:
-        y, x = int(loc[0][0] + th/2), int(loc[1][0] + tw/2)
-        subprocess.run(['ydotool', 'mousemove', str(x), str(y)])
-        subprocess.run(['ydotool', 'click', '1'])
-        time.sleep(0.4)                # small debounce
-    time.sleep(0.1)
+def ydotool(*cmd):
+    subprocess.run(
+        ['sudo', '/home/lan/src/cloned/gh/ReimuNotMoe/ydotool/build/ydotool', *cmd],
+        check=True
+    )
+
+if __name__ == '__main__':
+    tmpl = cv2.imread('retry_button.png', cv2.IMREAD_GRAYSCALE)
+    th, tw = tmpl.shape[::-1]
+    
+    while True:
+        gray = cv2.cvtColor(screenshot_ppm(), cv2.COLOR_BGR2GRAY)
+        res  = cv2.matchTemplate(gray, tmpl, cv2.TM_CCOEFF_NORMED)
+        loc  = np.where(res >= 0.93)
+        if loc[0].size:
+            y, x = int(loc[0][0] + th/2), int(loc[1][0] + tw/2)
+            ydotool('mousemove', '-a', '-x', str(x), '-y', str(y))
+            ydotool('click', '1')
+            time.sleep(0.4)                # small debounce
+        time.sleep(0.1)
```

2025-07-05 Wk 27 Sat - 02:10

So we got ydotool to run without sudo. New script:

(LLM chatgpt-4o)
```python
#!/usr/bin/env python3
import os
import time
import subprocess
import glob
import cv2
import numpy as np
from pathlib import Path

YTOOL = "/home/lan/src/cloned/gh/ReimuNotMoe/ydotool/build/ydotool"
SOCKET = "/home/lan/.ydotool_socket"
TEMPLATE = os.path.join(os.path.dirname(__file__), "retry_button.png")
CONFIDENCE = 0.93
POLL_INTERVAL = 0.25

def ydotool(*args):
    subprocess.run(
        [YTOOL, *args],
        env={**os.environ, "YDOTOOL_SOCKET": SOCKET},
        check=True
    )

def screenshot_ppm():
    subprocess.run([
        "gdbus", "call", "--session",
        "--dest", "org.freedesktop.portal.Desktop",
        "--object-path", "/org/freedesktop/portal/desktop",
        "--method", "org.freedesktop.portal.Screenshot.Screenshot",
        "''", "{}"
    ], check=True)

    time.sleep(1.0)  # allow time for screenshot to save
    pics = sorted(Path.home().joinpath("Pictures").glob("Screenshot*.png"), key=os.path.getmtime)
    if not pics:
        raise RuntimeError("No screenshot found in ~/Pictures")
    return cv2.imread(str(pics[-1]))

def main():
    tmpl = cv2.imread(TEMPLATE, cv2.IMREAD_GRAYSCALE)
    if tmpl is None:
        raise FileNotFoundError(f"Template image not found: {TEMPLATE}")
    th, tw = tmpl.shape[::-1]

    print("Watching for Retry button...")

    while True:
        img = screenshot_ppm()
        gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
        res = cv2.matchTemplate(gray, tmpl, cv2.TM_CCOEFF_NORMED)
        loc = np.where(res >= CONFIDENCE)

        if loc[0].size:
            y, x = int(loc[0][0] + th/2), int(loc[1][0] + tw/2)
            print(f"→ Button detected at ({x}, {y})")

            ydotool("mousemove", "-a", "-x", str(x), "-y", str(y))
            time.sleep(0.1)
            ydotool("click", "1")
            print("✓ Clicked Retry")

            time.sleep(0.5)  # debounce

        time.sleep(POLL_INTERVAL)

if __name__ == "__main__":
    main()
```
(/LLM chatgpt-4o)

2025-07-05 Wk 27 Sat - 02:21

Issue with coordinates... Taking a full screenshot and pasting in Pinta I get a coordinate of `(11, 523)`.

If I try to go there with

```python
ydotool("mousemove", "-a", "-x", "11", "-y", "523")
```

It takes me lower left corner instead of center left.

```python
ydotool("mousemove", "-a", "-x", "0", "-y", "0")
```

takes us to the upper left corner. `(0,0)`, which agrees with the screenshot coordinates. So this could be a scaling issue. The lowest coordinate in Pinta down is y=1079, yet ydotool seems to reach the bottom already at y=600.

x=1919 is furthest to the right in screenshot coordinates, yet we reach there at x=960. It seems to be scaling by two?

So to get to the lower right corner:

```sh
YDOTOOL_SOCKET="/home/lan/.ydotool_socket" /home/lan/src/cloned/gh/ReimuNotMoe/ydotool/build/ydotool mousemove -a -x 960 -y 540
```

Dividing by 2 got us close... but not quite! It's just under!


So changing coordinates to just the next button gets us to the correct button...

```
→ Button detected at (25, 508)
→ Button (Transformed) detected at (12, 254)
```

Pinta agrees with `(25, 508)` but for the next button! There's some y-shift, why?

```
th, tw = tmpl.shape[::-1]
y, x = int(loc[0][0] + th/2), int(loc[1][0] + tw/2)
```

These offsets.

```python
print(f"Centering shifts: {th} {tw}")

# output
Centering shifts: 110 33
```

This matches the dimensions of the second png we're testing (just the button above the original): 110 × 33 pixels.

So we're just aiming to be center of it.

It's a mixup... th is actually for width, not height. It's 110 pixels *wide* and yet that's reported for `th`. 


Then we're able to get it to click with

```python
ydotool("click", "0xC0")
```

2025-07-05 Wk 27 Sat - 02:57

```sh
cp test.sh test.-1.sh 
```

2025-07-05 Wk 27 Sat - 13:24

```sh
gdbus call --session --dest "org.freedesktop.portal.Desktop" --object-path "/org/freedesktop/portal/desktop" --method "org.freedesktop.portal.Screenshot.Screenshot" ""  "{'interactive': <false>, 'flash': <false>, 'cursor': <false>}"
```

This accepts invalid arguments. The options can be found [here](<https://flatpak.github.io/xdg-desktop-portal/docs/doc-org.freedesktop.portal.Screenshot.html>).

This flash can be disabled via toggling

```sh
gsettings set org.gnome.desktop.interface enable-animations false
```

2025-07-05 Wk 27 Sat - 13:52

The final script looks like

```python
#!/usr/bin/env python3
import os
import time
import subprocess
import glob
import cv2
import numpy as np
from pathlib import Path

YTOOL = "/home/lan/src/cloned/gh/ReimuNotMoe/ydotool/build/ydotool"
SOCKET = "/home/lan/.ydotool_socket"
TEMPLATE = os.path.join(os.path.dirname(__file__), "retry_button.png")
CONFIDENCE = 0.93
POLL_INTERVAL = 0.25

def ydotool(*args):
    subprocess.run(
        [YTOOL, *args],
        env={**os.environ, "YDOTOOL_SOCKET": SOCKET},
        check=True
    )

def screenshot_ppm():
    """
    Take a portal screenshot with animations temporarily disabled so the
    white flash never appears.  The original setting is restored afterward.
    """
    import subprocess, time, os
    from pathlib import Path

    shots_dir = Path.home() / "Pictures"

        # disable flash animation
    try:
        orig_anim = subprocess.check_output(
            ["gsettings", "get", "org.gnome.desktop.interface", "enable-animations"],
            text=True
        ).strip()           # returns 'true' or 'false'
    except Exception:
        orig_anim = None    # gsettings not available, continue silently

    restore_needed = (orig_anim == 'true')
    if restore_needed:
        subprocess.run(
            ["gsettings", "set",
             "org.gnome.desktop.interface", "enable-animations", "false"],
            check=True
        )

    try:
        before = {f.name for f in shots_dir.glob("Screenshot*.png")}
        subprocess.run([
            "gdbus", "call", "--session",
            "--dest", "org.freedesktop.portal.Desktop",
            "--object-path", "/org/freedesktop/portal/desktop",
            "--method", "org.freedesktop.portal.Screenshot.Screenshot",
            "",  "{'interactive': <false>}"
        ], check=True, stdout=subprocess.DEVNULL)

        # wait for the new file and for it to finish writing
        path = None
        for _ in range(20):          # up to 2 s
            time.sleep(0.1)
            new = {f.name for f in shots_dir.glob('Screenshot*.png')} - before
            if new:
                path = shots_dir / max(new)
                # settle loop
                for _ in range(10):
                    s1 = path.stat().st_size
                    time.sleep(0.05)
                    if s1 == path.stat().st_size and s1 > 0:
                        break
                break
        if not path or not path.exists():
            raise RuntimeError("Portal never produced a screenshot")

        img = cv2.imread(str(path))
        try:
            path.unlink()           # keep Pictures clean
        except OSError:
            pass
        if img is None:
            raise RuntimeError(f"Could not read {path}")

        return img

    finally:
				# restore previous animation settings
        if restore_needed:
            subprocess.run(
                ["gsettings", "set",
                 "org.gnome.desktop.interface", "enable-animations", "true"],
                check=True
            )


def main():
    tmpl = cv2.imread(TEMPLATE, cv2.IMREAD_GRAYSCALE)
    if tmpl is None:
        raise FileNotFoundError(f"Template image not found: {TEMPLATE}")
    tw, th = tmpl.shape[::-1]

    print("Watching for Retry button...")

    while True:
        img = screenshot_ppm()
        img_h, img_w = img.shape[:2]

        screen_w = 1920
        screen_h = 1080
        scale_x = screen_w / img_w
        scale_y = screen_h / img_h

        gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
        res = cv2.matchTemplate(gray, tmpl, cv2.TM_CCOEFF_NORMED)
        loc = np.where(res >= CONFIDENCE)

        if loc[0].size:
            y, x = int(loc[0][0] + th/2), int(loc[1][0] + tw/2)

            print(f"→ Button detected at ({x}, {y})")

            y, x = y // 2, x // 2

            print(f"→ Button (Transformed) detected at ({x}, {y})")

            # raw_y, raw_x = int(loc[0][0] + tw/2), int(loc[1][0] + th/2)
            #x = raw_x // 2
            #y = raw_y // 2

            print(f"→ Button detected at ({x}, {y})")

            ydotool("mousemove", "-a", "-x", str(x), "-y", str(y))
            time.sleep(0.1)
            ydotool("click", "0xC0")
            print("✓ Clicked Retry")

            time.sleep(0.5)  # debounce

        time.sleep(POLL_INTERVAL)

if __name__ == "__main__":
    # ydotool("mousemove", "-a", "-x", "0", "-y", "600")
    main()
```

It is able to take screenshots periodically, no flash, it deletes it as it creates it, and when it detects the image, it goes to the button and clicks it.

### 6.4.1 Trying to add visual feedback

2025-07-05 Wk 27 Sat - 14:00

It would be cool to draw a box around the detected area.

(LLM chatgpt-4o)

```sh
python3 -m pip install pygobject python-gtk-layer-shell
```

(/LLM chatgpt-4o)

```
../cairo/meson.build:31:12: ERROR: Dependency "cairo" not found, tried pkgconfig and cmake
```


(LLM chatgpt-4o)
```sh
sudo apt update
sudo apt install -y \
  build-essential meson pkg-config python3-dev \
  libcairo2-dev libgirepository1.0-dev \
  libgtk-3-dev gir1.2-gtk-3.0 \
  libgtk-layer-shell-dev
```
(/LLM chatgpt-4o)

```
../meson.build:31:9: ERROR: Dependency 'girepository-2.0' is required but not found.
```


(LLM chatgpt-4o)

```sh
sudo apt install libgirepository1.0-dev
```

(/LLM chatgpt-4o)

Should be

```sh
sudo apt install libgirepository-2.0-dev
```

```sh
python3 -m pip install pygobject python-gtk-layer-shell
```

```
ERROR: Could not find a version that satisfies the requirement python-gtk-layer-shell (from versions: none)
ERROR: No matching distribution found for python-gtk-layer-shell
```

2025-07-05 Wk 27 Sat - 14:25

We want to install [gtk-layer-shell](<https://github.com/wmww/gtk-layer-shell>) for python.

They say this only works on Wayland. And only on Wayland compositors that support the Layer Shell protocol. And Gnome-on-Wayland does not.

2025-07-05 Wk 27 Sat - 14:32

```sh
sudo apt install libcairo2-dev
python3 -m pip install python-xlib
python3 -m pip install pycairo

```

2025-07-05 Wk 27 Sat - 14:53

Some references:
- https://tronche.com/gui/x/xlib/
- https://github.com/python-xlib/python-xlib
- https://github.com/python-xlib/python-xlib/tree/master/examples

Let's try to run an example.

```sh
curl https://raw.githubusercontent.com/python-xlib/python-xlib/refs/heads/master/examples/draw-proto.py > draw-proto.py
chmod +x draw-proto.py
```

Have to change it to say `#!/usr/bin/python3` and install

```sh
python3 -m pip install Xlib
```

## 6.5 yodotool putting many ffffff

```sh
YDOTOOL_SOCKET="/home/lan/.ydotool_socket" /home/lan/src/cloned/gh/ReimuNotMoe/ydotool/build/ydotool "key" "33"
```

```
f% 
ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff
```

It will also not work the second time around unless your restart

```sh
# in /home/lan/src/cloned/gh/ReimuNotMoe/ydotool/build
./ydotoold --socket-path="$HOME/.ydotool_socket"
```

ydotoold also gives

```
WARNING: running xinput against an Xwayland server. See the xinput man page for details.
unable to find device pointer:ydotoold virtual device
```

Seems we can tell it to do keydown with :1 and keyup with :0

```sh
YDOTOOL_SOCKET="/home/lan/.ydotool_socket" /home/lan/src/cloned/gh/ReimuNotMoe/ydotool/build/ydotool key 33:1 33:0
```

# 7 Investigations

## 7.1 On [GTK](<https://www.gtk.org/>)

2025-07-05 Wk 27 Sat - 14:10

[Architecture](<https://www.gtk.org/docs/architecture/>).

![[Pasted image 20250705141154.png]]

# 8 References

