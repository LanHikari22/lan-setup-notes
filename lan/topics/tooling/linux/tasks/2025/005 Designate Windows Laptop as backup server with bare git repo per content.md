---
sourced_by: "[[Wk 29 000 Setting up bare git remote repo]]"
status: done
---
#obsidian


# 1 Objective

I share data back and forth between my laptop and my PC. It can go out of sync, and syncing both is tedious. 

I would like my PC to be light and easy to format where possible and to have good data organization.

For this, I would like to create windows bare git repositories to sync content across devices instead of just copying things around with `scp`. 

This includes my personal notes as I currently use `sshfs` to mount them from my laptop, but this introduces unpredictability in service of when the network mount would go down.

- [x] Note repository is cloned instead of `sshfs`'d
- [x] There exists new repositories for different content to clone from the laptop as a backup server.

# 2 Journal

2025-07-29 Wk 31 Tue - 07:00

Gonna have these under WSL2 in Windows for my laptop. Starting with private notes. Since they're private, they're not on github like this notes repo, but still should function in a similar way, and all my devices should be able to git clone it from the `~/data/git/notes.git`.

From [[Wk 29 000 Setting up bare git remote repo]],

![[Wk 29 000 Setting up bare git remote repo#^process]]

![[Wk 29 000 Setting up bare git remote repo#^add-ssh-key]]

2025-07-29 Wk 31 Tue - 11:21

Specifically for the notes repo,

We need to set windows SSH to be by default over bash (requires admin):  [[#^powershell-ssh-bash]]

```powershell
New-ItemProperty -Path "HKLM:\SOFTWARE\OpenSSH" -Name DefaultShell -Value "C:\Program Files\Git\bin\bash.exe" -PropertyType String -Force
```

```sh
# In the server (windows)
mkdir git
cd git
git init --bare notes.git

# In the client
mkdir -p ~/src/cloned/winlenovo
cd ~/src/cloned/winlenovo
git clone ssh://{user}@{server}/c/git/notes.git
cd notes
git branch -m master main
```

2025-07-29 Wk 31 Tue - 12:10

I copied the history over according to [[#5.3 Copy git history to new git repo]].

Now this is a proper git server solution for notes. We no longer need to use `sshfs`, and we can clone the notes within any device within the network and use obsidian from there.
# 3 Tasks

## 3.1 Clone project in phone and try to open it with mobile obsidian

- [x] 

2025-07-29 Wk 31 Tue - 13:17

2025-07-29 Wk 31 Tue - 21:41

We were able to do this with [termux](https://play.google.com/store/apps/details?id=com.termux&hl=en-US&pli=1) to just git clone to `/storage/emulated/0/src/cloned/winlenovo/prsonal-notes` and open that vault with the mobile obsidian!


# 4 Issues
## 4.1 Unable to login over SSH even after adding info to authorized_keys

- [x] 

2025-07-29 Wk 31 Tue - 07:21

See [[#5.1 Trace ssh logs for connection attempts]].

![[#^auth-linux]]

This is not showing any activity on WSL2. So this is not what we're trying to connect to.

2025-07-29 Wk 31 Tue - 11:15

So the `administrators_authorized_keys` in `%programdata%\ssh` can be edited as an administrator.

Even though we are unable to trace auth logs, this with using the correct windows user allowed login, so we know it is that service that is receiving connections now and now WSL2.

## 4.2 SSH links over windows directories

- [x] 

2025-07-29 Wk 31 Tue - 11:27

I can't seem to just do

```sh
git clone {user}@{server}:C:\Users\{user}\git\notes.git notes.git
```

That doesn't work.

From this [stackoverflow answer](https://stackoverflow.com/a/54106128/6944447),

I'm gonna try

```sh
git clone ssh://{user}@{server}/c/Users/{user}/git/notes.git
git clone ssh://{user}@{server}/c/git/notes.git
```

Neither short nor long paths work here.

They did mention to change the default shell to bash first... let's try.

```powershell
New-ItemProperty -Path "HKLM:\SOFTWARE\OpenSSH" -Name DefaultShell -Value "C:\Program Files\Git\bin\bash.exe" -PropertyType String -Force
```
^powershell-ssh-bash

This had to be done as admin.

Now when we SSH login, we're no longer in cmd, but in bash. And this works:

```sh
git clone ssh://{user}@{server}/c/git/notes.git
```

# 5 HowTos

## 5.1 Trace ssh logs for connection attempts

- [x]  Skipped

From this [tecmint blog post](https://www.tecmint.com/find-failed-ssh-login-attempts-in-linux/),

We can check `/var/log/auth.log` on Linux.

```sh
tail -f /var/log/auth.log
```
^auth-linux

They also mention

```sh
journalctl _SYSTEMD_UNIT=ssh.service
```

2025-07-29 Wk 31 Tue - 09:15

From [learn.microsoft blog on openssh server](https://learn.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse),

2025-07-29 Wk 31 Tue - 11:05

This [learn.microsoft heading](https://learn.microsoft.com/en-us/windows-server/administration/OpenSSH/openssh-server-configuration#syslogfacility) refers us to `%programdata%\ssh\logs`. That folder requires admin rights to access. However it's empty.

Is it just not logging?

But it says

```
# Logging
#SyslogFacility AUTH
LogLevel DEBUG
```

in `sshd_config` under `%programdata%\ssh`.

### 5.1.1 Skipped

## 5.2 Rename git branch from master to main

- [x] 

2025-07-29 Wk 31 Tue - 11:44

From this [blog](https://www.git-tower.com/learn/git/faq/git-rename-master-to-main/),

```sh
git branch -m master main
```
^rename-branch

## 5.3 Copy git history to new git repo

- [x] 

2025-07-29 Wk 31 Tue - 11:50

Hmm. Simply copying the `.git/objects` content over does not result in carrying over of git history to a new repository.

Similar to this [stackoverflow answer](https://stackoverflow.com/a/17373088/6944447),

Copy over previous notes repository in as `notes-old/`. 

```sh
# in /home/lan/src/cloned/winlenovo/notes
git remote add mirror ~/src/cloned/winlenovo/notes-old
git fetch mirror
git merge mirror/master --allow-unrelated-histories
git remote rm mirror
```

2025-07-29 Wk 31 Tue - 12:06

Yikes.  Should've copied the main branch... Let's try again with main

Okay it seems fine.

## 5.4 Compress folder into a tar archive

- [x] 

2025-07-29 Wk 31 Tue - 21:48

I need to move an embedded `.git` into a tar archive since that inner repository is no longer relevant.

From [howtogeek](https://www.howtogeek.com/248780/how-to-compress-and-extract-files-using-the-tar-command-on-linux/),

```sh
tar -czvf name-of-archive.tar.gz /path/to/directory-or-file
```
^compress-tar-gz

In my case

```sh
tar -czvf git_2025_Wk31.tar.gz .git
```

# 6 Ideas

## 6.1 Asking and answering same question on stackoverflow

2025-07-29 Wk 31 Tue - 09:12

This [blog post](https://stackoverflow.blog/2011/07/01/its-ok-to-ask-and-answer-your-own-questions/) explains that this is encouraged.

I run into many issues in these notes so it may be a good idea, especially if the issue is general enough.

2025-07-29 Wk 31 Tue - 10:39

Tried to ask on [[#3.1 Unable to login over SSH even after adding info to authorized_keys]] but removed due to it being judged off topic. I guess it needs to be a programming task, and this is more operative? 

It did match the condition of being a relevant tool to programmers, but not the condition of being unique to programming, so a different stack exchange would be better.

This would be a better place [superuser on-topic](https://superuser.com/help/on-topic).