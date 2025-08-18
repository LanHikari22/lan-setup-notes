---
status: todo
---

# 1 Objective

Previously, we have [[005 Designate Windows Laptop as backup server with bare git repo per content#3.1 Clone project in phone and try to open it with mobile obsidian|cloned an obsidian vault in mobile to have our notes on all devices]].

This solution is functional, however it would be good to also have seamless content sync between devices. We ran into an issue that terminal access in the phone with termux was quite limited without rooting. 

The goal here is to root our device or install an open source OS and achieve sshfs sync with the phone.

- [ ] Achieve seamless sshfs-based sync of obsidian vaults on phone


See also [[000 Install open source mobile OS on my phone]].


# 2 Tasks

## 2.1 Get Obsidian to sync on phone via sshfs

- [ ] 

2025-08-17 Wk 33 Sun - 03:27

See [[000 Install open source mobile OS on my phone#3.2 Install LineageOS on my Pixel 3]] for OS installation and rooting with magisk.

We can also do all this from PC now with SSH rooted remote access to our phone with termux. See [[000 Install open source mobile OS on my phone#3.3 Getting SSH remote access to my Pixel 3]]

2025-08-17 Wk 33 Sun - 03:**45**

Can't just do  `pkg install sshfs`

```sh
sshfs

# out
No command sshfs found, did you mean:
 Command sshfp in package hash-slinger
```

```sh
pkg install hash-slinger
pkg install mandoc
```

```sh
man sshfp

# out (relevant)
NAME
       sshfp - Generate SSHFP DNS records from knownhosts files or ssh-keyscan
	   
DESCRIPTION
       sshfp generates RFC-4255 SSHFP DNS records based on the public keys
       stored in a known_hosts file, which implies the user has previously
       trusted this key, or public keys can be obtained by using ssh-keyscan
       (1). Using ssh-keyscan (1) implies a secure path to connect to the
       hosts being scanned. It also implies a trust in the DNS to obtain the
       IP address of the hostname to be scanned. If the nameserver of the
       domain allows zone transfers (AXFR), an entire domain can be processed
       for all its A records.
```

Not what we're looking for.

There is a `termux-sshfs`, on github, but it's outdated 5 years and there's a data deletion bug there: [#3](https://github.com/neiviv-ui/termux-sshfs/issues/3) 





2025-08-17 Wk 33 Sun - 04:00

![[Pasted image 20250817040043.png]]

[Termux Wiki](https://wiki.termux.com/wiki).

Maybe an entry on this should exist.

