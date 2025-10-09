# 1 Wk 29 001 - Resolving Qemu and Docker issues for Windows Docker Build

This is for [this issue](../../../topics/tooling/linux/tasks/2025/003%20Run%20Windows%20games%20on%20Docker%20or%20VM%20in%20Linux%20if%20wine%20fails.md#41-cant-create-bind-file-on-docker-compose-up-and-qemu-file-error).

# 2 Issue

````
emu-windows-1  | /init.sh: line 9: can't create /sys/class/vtconsole/vtcon0/bind: Read-only file system
emu-windows-1  | qemu-system-x86_64 -monitor stdio -smp 2,cores=2,threads=1,sockets=1 -m 3G -net user,hostfwd=tcp::3389-:3389 -net nic,model=e1000 -audiodev pa,id=pa -usb -device usb-tablet -usb -device usb-mouse -drive /disk.qcow2 -vnc 0.0.0.0:0
emu-windows-1  | qemu-system-x86_64: -drive /disk.qcow2: warning: short-form boolean option '/disk.qcow2' deprecated
emu-windows-1  | Please use /disk.qcow2=on instead
emu-windows-1  | qemu-system-x86_64: -drive /disk.qcow2: Must specify either driver or file
````

init.sh will be pasted.

# 3 Objective

Be able to start up system with docker compose up

# 4 Solution

# 5 LLM Instructions

* This is a diagnostic document and not a conversation. Everything shared is context. Address the questions tagged (Q#) like (Q1) for example. If you see something like (~1), assume it part of the archive and not a latest set of questions.
  * Since it keeps occurring, I ask Again
  * !!! NEVER RESPOND TO (~1), (~2), etc.
  * ONLY respond to the tagged questions. Nothing else.

(LLM chatgpt-4o)
(Note: Between these tags is responses from ChatGPT-4o.
(/LLM chatgpt-4o)

(LLM chatgpt-4o)
(/LLM chatgpt-4o)

# 6 Journal

(Q 20:40) Two issues, the bind and the drive option for qemu

It seems trying to write to `/sys` affects the host system? Not sure if  this is necessary, and if so, might be best done outside docker?

## 6.1 On VNC

2025-07-19 Wk 29 Sat - 22:17

(LLM chatgpt-4o)
Accessing VNC protocol via browser is not supported since it can't interpret VNC protocol directly.
(/LLM chatgpt-4o)

## 6.2 On cdrom drives

(LLM chatgpt-4o)

Instead of `-cdrom` which conflicts on being used twice, use

````
# Main virtual disk
-drive file=/disk.qcow2,if=ide,format=qcow2

# Windows ISO (bootable)
-drive file=/iso/win10.iso,media=cdrom,index=2

# VirtIO driver ISO
-drive file=/iso/virtio.iso,media=cdrom,index=3
````

(/LLM chatgpt-4o)

# 7 References
