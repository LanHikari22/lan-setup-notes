---
status: done
---

\#lan #task #issue #linux #ubuntu #clipboard #vim #st/done

# 1 Objective

* [x] In nvim, Copying or "+ yanking should alter the clipboard.
* [x] Tmux `Prefix+[` should also alter the clipboard

# 2 Journal

## 2.1 Nvim yanking to clipboard

2025-07-18 Wk 29 Fri - 23:58

In [Accessing the system clipboard](https://vim.fandom.com/wiki/Accessing_the_system_clipboard) [<a name="1" />^1](001%20Fix%20yanking%20in%20nvim%20and%20tmux%20not%20going%20to%20clipboard%20in%20Ubuntu%2025.04.md#1),

````sh
vim --version | grep clipboard
````

lets us see if there is clipboard support. We know we were able to copypaste with nvim before...

For vim, we don't have clipboard enabled. We're also in Wayland.

nvim doesn't show features this way.

````
:set clipboard
````

Gives nothing. Settiing it to unnamedplus doesn't change behavior. And that clearly mentions X11.

In [Arch Neovim](https://wiki.archlinux.org/title/Neovim) [<a name="2" />^2](001%20Fix%20yanking%20in%20nvim%20and%20tmux%20not%20going%20to%20clipboard%20in%20Ubuntu%2025.04.md#2) they mention:

 > 
 > you may need to install [xclip](https://archlinux.org/packages/?name=xclip) (X11) or [wl-clipboard](https://archlinux.org/packages/?name=wl-clipboard) (Wayland).

````sh
sudo apt install wl-clipboard
````

This works for nvim! Now even though `clipboard` is empty in `:set clipboard`, doing `"+y` yanking will affect clipboard!

## 2.2 Tmux yanking

When doing `Prefix+[` and yanking with it, I get

````
Error! tmux-yank dependencies not installed!
````

Let's try to install and upgrade all tmux plugins as I've noted in [`~/.tmux.conf`](https://github.com/LanHikari22/lan-dotfiles/blob/master/tmux.conf),

 > 
 > to install plugins: prefix - I. Update them with prefix - U.

prefix - U and "all".

Seems  to be good now!

# 3 References

1. [Accessing the system clipboard](https://vim.fandom.com/wiki/Accessing_the_system_clipboard) ^1
1. [Arch Neovim](https://wiki.archlinux.org/title/Neovim) ^2

**Search**

1. https://stackoverflow.com/questions/11489428/how-can-i-make-vim-paste-from-and-copy-to-the-systems-clipboard
1. https://hatchjs.com/neovim-copy-to-system-clipboard/
