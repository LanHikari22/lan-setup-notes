---
status: done
---

# 1 Objective

I would like to have full control of one of my phones at least. I have...

|Device|Specs|EOL|
|------|-----|---|
|Galaxy A54 5G, SM-A546E/DS|[spec2](https://www.phonemore.com/specs/samsung/galaxy-a54/sm-a546eds-256gb/)||
|Pixel 3|[spec1](https://deviceguides.ee.co.uk/google/pixel-3-android-9-0/specifications/), [spec2](https://www.phonemore.com/specs/google/pixel-3/)|Yes|
|Galaxy Tab A6 SM-T280||Yes|
|<a name="phone-spec-table" />^phone-spec-table|||

[Spec comparison](https://www.phonemore.com/compare/phones/samsung-galaxy-a54-sm-a546eds-256gb-vs-google-pixel-3-64gb/23084726).

I need to pick an open source OS and install it on one of them.

* [x] Installed an open source OS on at least one of my phones

Related,
[003 Achieve Obsidian Notes Sync between my mobile phone and PC](../../../obsidian/tasks/2025/003%20Achieve%20Obsidian%20Notes%20Sync%20between%20my%20mobile%20phone%20and%20PC.md).

# 2 Journal

2025-08-05 Wk 32 Tue - 13:29

My [Pixel 3](https://www.phonemore.com/specs/google/pixel-3/) is more outdated, from 2018. It's lagging, so it's in need of upgrade more, but it also has weaker specs.

But also unlike my [Galaxy A54](https://www.phonemore.com/specs/samsung/galaxy-a54/sm-a546eds-256gb/)  Pixel 3 is supported by [GrapheneOS](https://grapheneos.org/).

# 3 Tasks

## 3.1 Install GrapheneOS on my Pixel 3

* [x] Out of support. See [3.2 Install LineageOS on my Pixel 3](000%20Install%20open%20source%20mobile%20OS%20on%20my%20phone.md#32-install-lineageos-on-my-pixel-3)

2025-08-16 Wk 33 Sat - 12:24

I backed up and factory reset the device. We should be able to install now.

[GrapheneOS Install guide](https://grapheneos.org/install/).

2025-08-16 Wk 33 Sat - 12:32

Android version: 12
Android security update: October 5, 2021.

There are no more updates, security or otherwise by Google for this.

2025-08-16 Wk 33 Sat - 12:34

Following [GrapheneOS cli install guide](https://grapheneos.org/install/cli),

````sh
sudo apt install libarchive-tools
curl -O https://dl.google.com/android/repository/platform-tools_r35.0.2-linux.zip
echo 'acfdcccb123a8718c46c46c059b2f621140194e5ec1ac9d81715be3d6ab6cd0a  platform-tools_r35.0.2-linux.zip' | sha256sum -c
bsdtar xvf platform-tools_r35.0.2-linux.zip
````

````sh
export PATH="$PWD/platform-tools:$PATH"
````

````sh
fastboot --version

# out
fastboot version 35.0.2-12147458
Installed as /home/lan/platform-tools/fastboot
````

````sh
sudo apt install android-sdk-platform-tools-common
````

````sh
sudo systemctl stop fwupd.service
````

2025-08-16 Wk 33 Sat - 12:42

Ooh rebooting my phone and holding the volume down button put me in Fastboot Mode!

````sh
fastboot flashing unlock

# out
< waiting for any device >
````

Hmm nothing. The guide did mention that using the front USB-C panel is often broken for installations like this, let's try connecting USB-C to the back of the PC

2025-08-16 Wk 33 Sat - 12:56

````sh
fastboot flashing unlock                          

# out
OKAY [  0.083s]
Finished. Total time: 0.083s
````

This changed the view on my phone, to the message `"If you unlock the bootloader, you will be able to install custom operating system software on this phone. [...]`

Unlocking with volume button up and power button...

Now it says

````
Device state: unlocked
````

Was locked before.

I have this already, but

````sh
sudo apt install openssh-client
````

````sh
curl -O https://releases.grapheneos.org/allowed_signers

````

````sh
echo "contact@grapheneos.org ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIIUg/m5CoP83b0rfSCzYSVA4cw4ir49io5GPoxbgxdJE" > expected
diff -u allowed_signers expected

# out
[nothing: files are identical]
````

2025-08-16 Wk 33 Sat - 13:04

From the [releases](https://grapheneos.org/releases) page, we want Pixel 3, but it's not here.

It has many string matches in the rather long changelogs... It's beyond legacy support it seems. But I thought I read GrapheneOS could still be installed on this device.

Pixel 4 has those links:

````
https://releases.grapheneos.org/flame-factory-2025012701.zip
https://releases.grapheneos.org/flame-factory-2025012701.zip.sig
https://releases.grapheneos.org/flame-ota_update-2025012701.zip
````

Searching `Pixel 3`, the first result leads us to github releases [SP1A.210812.016.C2.2022081800](https://github.com/GrapheneOS/platform_manifest/releases/tag/SP1A.210812.016.C2.2022081800)

Doesn't seem like what we want.

Right they do say in [device support](https://grapheneos.org/faq#device-support) that

````
 The following end-of-life devices are no longer supported:
- Pixel 3a XL (bonito)
- Pixel 3a (sargo)
- Pixel 3 XL (crosshatch)
- Pixel 3 (blueline)
````

I know I have blueline for this...

It's no longer supported, and even if we were to experiment with the device and see what GrapheneOS is like, the OS itself will be out of date.

Let's try another option.

[3.2 Install LineageOS on my Pixel 3](000%20Install%20open%20source%20mobile%20OS%20on%20my%20phone.md#32-install-lineageos-on-my-pixel-3)

## 3.2 Install LineageOS on my Pixel 3

* [x] 

2025-08-16 Wk 33 Sat - 14:06

Seems the device is [supported](https://wiki.lineageos.org/devices/blueline/) for this.

From [Installing Lineage Recovery using `fastboot`](https://wiki.lineageos.org/devices/blueline/install/#installing-lineage-recovery-using-fastboot),

[Downloading boot.img](https://mirrorbits.lineageos.org/full/blueline/20250811/boot.img).

````sh
fastboot flash boot ~/Downloads/boot.img

# out
Sending 'boot_b' (65536 KB)                        OKAY [  1.640s]
Writing 'boot_b'                                   OKAY [  0.412s]
Finished. Total time: 2.370s
````

Selecting `Recovery Mode` option on the device

We can see the logo of LineageOS on the device.

[Downloading the image zip file](https://mirrorbits.lineageos.org/full/blueline/20250811/lineage-22.2-20250811-nightly-blueline-signed.zip) from [blueline/builds](https://download.lineageos.org/devices/blueline/builds).

Select this from the device first (Apply Update -> Apply from ADB) then do

````
adb -d sideload ~/Downloads/lineage-22.2-20250811-nightly-blueline-signed.zip
````

When it asks about rebooting the bootloader to install some packages say yes and reado this step to sideload

Repeat again... x1

I hope I'm not just restarting the process... I don't see indication of progress between steps here...

I think we're repeating. Do reboot system now.

Seedvault backup... don't have any

2025-08-16 Wk 33 Sat - 14:53

So we have the OS now! But no apps or anything. We also didn't get Play Store.

There are strange black spots on the back camera view though.

A recommendation I see is to use [microg](https://github.com/microg) instead of the play store. [MicroG for LineageOS](https://lineage.microg.org/)

I guess we wiill have to reinstall for this though.

[this reddit post](https://www.reddit.com/r/LineageOS/comments/mhov7b/local_terminal_on_171/) mentions there used to be a local terminal app and killed, apparently by an author of LineageOS. We need to get some sort of terminal. Maybe Termux APK.

`ls /` on Termux still says permission denied like with stock OS.

2025-08-16 Wk 33 Sat - 15:22

We still need magisk. some hints in this [forum](https://xdaforums.com/t/guide-magisk-on-lineageos.4371987/).

We can get the latest magisk apk [in github releases](https://github.com/topjohnwu/Magisk/releases).

Following [Magisk install](https://topjohnwu.github.io/Magisk/install.html),

So we need the boot.img from before in our device now...

[Downloading boot.img](https://mirrorbits.lineageos.org/full/blueline/20250811/boot.img).

Not able to `adb pull` the patched boot.img...

````
adb: error: failed to get feature set: no devices/emulators found
````

In the bootloader when enabling ADB it says

````
adb: error: failed to stat remote object '/storage/emulated/0/Download/magisk_patched-{random string}.img': No such file or directory
````

Even though that's the link it reported.

In termux we actually don't see anything in that folder!

Not in`/sdcard/Downloads` either...

I can get the file myself on my pc through file transfer USB setting.

Reboot back into fastboot mode and

````
fastboot flash boot /path/to/magisk_patched_[random_strings].img
````

Now android just says

````
No command
````

![Pasted image 20250816160539.png](../../../../../../attachments/Pasted%20image%2020250816160539.png)
[image source](https://www.gtricks.com/android/how-to-fix-android-no-command-error/)

After trying to restart the bootloader...

This [reddit answer](https://www.reddit.com/r/androidroot/comments/jf08h8/comment/g9imgjh/?utm_source=share&utm_medium=web3x&utm_name=web3xcss&utm_term=1&utm_content=share_button) explains that holding the power and volume  up key gets us out of that screen, which it does, but we just get back to it.

Now I see some `Waiting for rescue commands...`

This may be similar [Rescue mode Pixel 5A Lineage OS 22.2 Android 15](https://xdaforums.com/t/rescue-mode-pixel-5a-lineage-os-22-2-android-15.4744562/)

2025-08-16 Wk 33 Sat - 16:04

Somehow without much besides power and volume up key we are able to get back to the OS.

2025-08-16 Wk 33 Sat - 16:09

We're rooted now! As explained in this [post](https://xdaforums.com/t/how-to-grant-termux-superuser-rights.4241671/), just have to do `su` in termux and grant it superuser permission!

2025-08-16 Wk 33 Sat - 18:03

To look into the lineageOS source [Build LineageOS for Google Pixel 3](https://wiki.lineageos.org/devices/blueline/build/)

The code is across 2.9k repositories [in github](https://github.com/orgs/LineageOS/repositories)

They have instructions for cloning also in [contributing-apps](https://wiki.lineageos.org/how-to/contributing-apps/#cloning-the-source-code)

[here](https://wiki.lineageos.org/emulator#initialize-the-lineageos-source-repository) they mention

````sh
cd ~/android/lineage
repo init -u https://github.com/LineageOS/android.git -b lineage-22.2 --git-lfs --no-clone-bundle
````

## 3.3 Getting SSH remote access to my Pixel 3

* [x] 

2025-08-16 Wk 33 Sat - 16:18

So we can do `pkg install openssh` in Termux, but we have to be outside `su`.

Installing and uninstalling tsu, does not seem to recognize we're rooted. Was recommended when I tried to do `sudo ls /`.

`pkg install termux-services` for `sv-enable sshd`

`pkg i termux-services` is also a shorthand for the `runit` service manager.

Those are the openssh instructions.

2025-08-16 Wk 33 Sat - 16:32

[termux remote access guide](https://wiki.termux.com/wiki/Remote_Access)

2025-08-17 Wk 33 Sun - 03:17

With setting `passwd`, and seeing the user with `ls -al`, and realizing this is running on port 8022, we have SSH access!

# 4 Issues

# 5 Side Notes

## 5.1 On Magisk

2025-08-16 Wk 33 Sat - 15:24

\[Magisk releases\],

 > 
 > * \[Core\] Migrate module implementation to Rust

They use Rust!

# 6 References
