---
sourced_by: "[[Wk 29 000 Setting up bare git remote repo]]"
status: todo
---
#external 


# 1 Objective

I share data back and forth between my laptop and my PC. It can go out of sync, and syncing both is tedious. 

I would like my PC to be light and easy to format where possible and to have good data organization.

For this, I would like to create windows bare git repositories to sync content across devices instead of just copying things around with `scp`. 

This includes my personal notes as I currently use `sshfs` to mount them from my laptop, but this introduces unpredictability in service of when the network mount would go down.

- [ ] Note repository is cloned instead of `sshfs`'d
- [ ] There exists new repositories for different content to clone from the laptop as a backup server.

# 2 Journal

2025-07-29 Wk 31 Tue - 07:00

Gonna have these under WSL2 in Windows for my laptop. Starting with private notes. Since they're private, they're not on github like this notes repo, but still should function in a similar way, and all my devices should be able to git clone it from the `~/data/git/notes.git`.

From [[Wk 29 000 Setting up bare git remote repo]],

![[Wk 29 000 Setting up bare git remote repo#^process]]

![[Wk 29 000 Setting up bare git remote repo#^add-ssh-key]]


# 3 Issues
## 3.1 Unable to login over SSH even after adding info to authorized_keys

- [ ] 

2025-07-29 Wk 31 Tue - 07:21

See [[#4.1 Trace ssh logs for connection attempts]].

![[#^auth-linux]]

This is not showing any activity on WSL2. So this is not what we're trying to connect to.

### 3.1.1 Pend

# 4 HowTos

## 4.1 Trace ssh logs for connection attempts

- [ ] 

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

### 4.1.1 Pend

# 5 Ideas

## 5.1 Asking and answering same question on stackoverflow

2025-07-29 Wk 31 Tue - 09:12

This [blog post](https://stackoverflow.blog/2011/07/01/its-ok-to-ask-and-answer-your-own-questions/) explains that this is encouraged.

I run into many issues in these notes so it may be a good idea, especially if the issue is general enough.

In line with this, I posted a [stack overflow question](https://stackoverflow.com/questions/79718205/how-to-view-ssh-logs-and-auth-attempts-on-windows) for [[#3.1 Unable to login over SSH even after adding info to authorized_keys]].

