---
status: archived
---

\#lan #task #st/archived

2025-07-02 Wk 27 Wed - 06:09

# 1 Objective

Would like to be able to access my system locally using xrdp and sunshine for higher frame rates.

* [x] Able to connect via xrdp and start new desktop session
* [ ] Able to connect via sunshine to existing desktop session

# 2 Journal

## 2.1 System Context

2025-07-03 Wk 27 Thu - 00:02

````sh
lsb_release -a

# output
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 25.04
Release:        25.04
Codename:       plucky
````

## 2.2 Attempt to setup xrdp

2025-07-02 Wk 27 Wed - 04:27

I remember before I had to create a virtual desktop to get sunshine to work when no screen was connected to my PC. It is not the case for me this time.

2025-07-02 Wk 27 Wed - 05:34

From [github: neutrinolabs/xrdp](https://github.com/neutrinolabs/xrdp) [<a name="1" />^1](000%20Setup%20remote%20access%20to%20ubuntu%20system.md#1),

````sh
sudo apt install xrdp
````

From [post on setting up xrdp on ubuntu 2](https://thelinuxcode.com/install_xrdp_server_ubuntu/) [<a name="3" />^3](000%20Setup%20remote%20access%20to%20ubuntu%20system.md#3),

SSH Tunnel for security

````sh
ssh -L 3389:localhost:3389 user@ubuntu-server
````

https://github.com/LizardByte/Sunshine/issues/3524

````
cd /usr/lib/x86_64-linux-gnu
sudo ln -s libminiupnpc.so.18 libminiupnpc.so.17
````

````
sunshine: error while loading shared libraries: libicuuc.so.70: cannot open shared object file: No such file or directory
````

https://stackoverflow.com/questions/35840359/libicuuc-so-55-cannot-open-shared-object-file

````
sudo apt-get install libicu-dev
````

didn't help

````
wget http://security.ubuntu.com/ubuntu/pool/main/i/icu/libicu55_55.1-7_amd64.deb
sudo dpkg -i libicu55_55.1-7_amd64.deb
sudo apt-get -f install
````

No effect.

2025-07-03 Wk 27 Thu - 00:08

Some things we've attempted:

````sh
sudo ufw allow from 127.0.0.1 to any port 3389
````

ensuring firewall allows localhost connections for xrdp port.

````sh
cat /etc/xrdp/startwm.sh                  

# output
[...]
startxfce4
````

But when we try to connect, we just get a black page.

2025-07-03 Wk 27 Thu - 00:18

In [forum on xfce black screen](https://bbs.archlinux.org/viewtopic.php?id=229484) [<a name="6" />^6](000%20Setup%20remote%20access%20to%20ubuntu%20system.md#6), `MrHritik` used

````
startxfce4 -display :1 -- :1 vt4
````

in a different tty. I could switch to tty3 on my system with Ctrl+Alt+F3 and come back to gnome with Ctrl+Alt+F2.

I'm not able to start this on this display though, since I'm already connected via my gnome desktop environment...

2025-07-03 Wk 27 Thu - 00:04

Following [blog on install xrdp on Ubuntu 25.04](https://www.server-world.info/en/note?os=Ubuntu_25.04&p=desktop&f=6) [<a name="5" />^5](000%20Setup%20remote%20access%20to%20ubuntu%20system.md#5),

Interesting commands they have:

````
[apt](https://www.server-world.info/en/note?p=command&q=apt) -y install xrdp tigervnc-standalone-server
[systemctl](https://www.server-world.info/en/note?p=command&q=systemctl)
````

````sh
apt -y install xrdp tigervnc-standalone-server
systemctl enable xrdp
````

````

[apt](https://www.server-world.info/en/note?p=command&q=apt) -y install xrdp tigervnc-standalone-server

root@dlp:~#

[systemctl](https://www.server-world.info/en/note?p=command&q=systemctl) enable xrdp
````

````sh
# in ~/startwm.sh
````

## 2.3 Attempt to setup vnc server

Following with [blog on installing vnc on ubuntu 24.04 LTS](https://idroot.us/install-vnc-server-ubuntu-24-04/) [<a name="4" />^4](000%20Setup%20remote%20access%20to%20ubuntu%20system.md#4),

````sh
sudo apt install xfce4 xfce4-goodies -y
sudo apt install tigervnc-standalone-server tigervnc-common -y
vncserver --version

# output
Xvnc TigerVNC 1.14.1 - built 2024-11-10 17:57
Copyright (C) 1999-2024 TigerVNC Team and many others (see README.rst)
See https://www.tigervnc.org for information on TigerVNC.
Underlying X server release 12101014
````

````sh
vncpasswd
````

For my system, encrypted password is in `~/.config/tigervnc/passwd`.

````sh
vncserver
vncserver -kill :1
````

This does not create a `~/.vnc/xstartup` as expected by the tutorial... Also unclear why we need to be using `xrdb` here.

````sh
# set ~/.vnc/xstartup to
#!/bin/sh
xrdb $HOME/.Xresources
startxfce4 &
````

````sh
vncserver -localhost no
````

## 2.4 Status

2025-07-17 Wk 29 Thu - 21:31

We are able to connect via xfce and set resolution in remmina.

# 3 References

1. [github: neutrinolabs/xrdp](https://github.com/neutrinolabs/xrdp) ^1
1. [post on setting up xrdp on ubuntu](https://www.digitalocean.com/community/tutorials/how-to-enable-remote-desktop-protocol-using-xrdp-on-ubuntu-22-04) <a name="2" />^2
1. [post on setting up xrdp on ubuntu 2](https://thelinuxcode.com/install_xrdp_server_ubuntu/) ^3
1. [blog on installing vnc on ubuntu 24.04 LTS](https://idroot.us/install-vnc-server-ubuntu-24-04/) ^4
1. [blog on install xrdp on Ubuntu 25.04](https://www.server-world.info/en/note?os=Ubuntu_25.04&p=desktop&f=6) ^5
1. [forum on xfce black screen](https://bbs.archlinux.org/viewtopic.php?id=229484) ^6
