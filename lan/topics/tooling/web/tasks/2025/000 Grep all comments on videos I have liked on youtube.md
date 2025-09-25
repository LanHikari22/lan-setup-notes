---
status: pend
---

\#lan #task #todo #youtube #grep #search #tooling #linux #visidata #docker

# 1 Objective

Searching the stream of time is difficult. There is this one video from 5+ years I want to find, and all I have is tiny keyword memories. Searching youtube itself would be enormous. Searching my playlists and liked lists is enough.

I would like to

* [ ] Grep all the videos I liked by title and get the url.
* [ ] Grep all comments in said videos for keywords.
* [ ] Grep all comments in a specific video for keywords.
* [ ] Use it easy with a docker CLI interface. No installation, no fuss. Reproducible.
* [ ] Publish it for now under `delta-box`. A project of reproducible container projects. This could be `box000`.

# 2 Solution

# 3 Journal

2025-07-02 Wk 27 Wed - 16:20

Any LLM use goes [here](../../../../../llm/weekly/2025/Wk%2027%20000%20Grep%20all%20comments%20on%20videos%20I%20have%20liked%20on%20youtube.md).

2025-07-19 Wk 29 Sat - 00:25

I have done this before. I should see what I used in my prior scripts.

## 3.1 Trying to use the old scripts again

2025-07-19 Wk 29 Sat - 05:21

Since we did [002 Creating base system for installation testing](../../../linux/tasks/2025/002%20Creating%20base%20system%20for%20installation%20testing.md), we now have box000 for a blank system to test on!

# 4 Techniques

## 4.1 Visidata Word Wrapping

* [x] 

2025-07-19 Wk 29 Sat - 17:04

From [discussion #1080](https://github.com/saulpw/visidata/discussions/1080),

You can press "v" to wrap text for the current row or toggle it.

v is not in the  [cheat sheet](https://jsvine.github.io/visidata-cheat-sheet/en/)...

Not in the [reference](https://www.visidata.org/docs/columns/) either...

I added a comment about that in [discussion #1080](https://github.com/saulpw/visidata/discussions/1080).

2025-07-19 Wk 29 Sat - 17:40

I have an old private entry `Adding keybinding to visidata to quickly view text` from 2023 Wk 43.

It expands text by modifying `~/.visidatarc`,

````python
@Sheet.api
def expand_cell(sheet):
	sheet.execCommand('dive-cell')
	new_sheet = vd.sheets[0]
	new_sheet.execCommand('visibility-sheet')

# `sheet` members and `vd` members are available in the execstr scope
BaseSheet.addCommand(None, 'expand-cell', 'expand_cell()', 'Expands cell')
BaseSheet.bindKey('KEY_F(2)', 'expand-cell')
````

Also comments on z + enter. This opens the text itself, and then v can be used. Adding to [discussion #1080](https://github.com/saulpw/visidata/discussions/1080)

Seems I've discovered that from the [source pyobj](https://github.com/saulpw/visidata/blob/e9006352929ebe93ecababe884987ee885b25b8b/visidata/pyobj.py#L276).

````sh
TAG="ubuntu_25.04" MOUNT="." sh <(curl -sSf https://raw.githubusercontent.com/{my_org}/{my_repo}/refs/heads/main/box/box000_blank_system/docker_root_sh.sh)
````

# 5 References

1. [gh yt-cli](https://github.com/BishrGhalil/yt-cli) <a name="1" />^1
