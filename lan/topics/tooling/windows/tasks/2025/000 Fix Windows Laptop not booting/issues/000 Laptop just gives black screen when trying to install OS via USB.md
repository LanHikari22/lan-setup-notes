---
parent: '[[000 Fix Windows Laptop not booting]]'
spawns: '[[001 Windows 11 USB Drive burnt with dd not recognized by Lenovo Laptop]]'
status: done
---

Parent: [000 Fix Windows Laptop not booting](../000%20Fix%20Windows%20Laptop%20not%20booting.md)

Spawned from [000 Fix Windows Laptop not booting > <a name="spawn-issue-4d3bdf" />^spawn-issue-4d3bdf](../000%20Fix%20Windows%20Laptop%20not%20booting.md#spawn-issue-4d3bdf)

# 1 Issue

I do not see the logo, which usually says Lenovo.

I have tried on boot F12, ESC, F11, F10 but I get nowhere.

When you turn on the device, the backlight goes on, so you know it's not powered off. And the fans start spinning.

The is a Lenovo Legion 5 device.  The code is Legion 5-15IMH05H ([amazon](https://www.amazon.com/Lenovo-81Y6000DUS-i7-10750H-1920x1080-Bluetooth/dp/B08D9S7KJ6), [lenovo](https://pcsupport.lenovo.com/us/en/products/laptops-and-netbooks/legion-series/legion-5-15imh05h/documentation/doc_userguide))

# 2 Journal

2025-09-09 Wk 37 Tue - 01:19 +03:00

In [Lenovo5-15IMH05H User Guide](https://download.lenovo.com/consumer/mobiles_pub/legion_5_15_17_ug_en_202004.pdf) page 20 section "Open the UEFI/BIOS setup utility",

They mention

 > 
 > When the Lenovo logo appears on the screen, press F2 repeatedly.

I suspect the laptop keyboard itself to be broken, but this does not work on it or on an external keyboard.

I think it is usually the case that the logo has to appear before such keys are registered. But the logo does not even appear.

2025-09-09 Wk 37 Tue - 01:31 +03:00

After a while, the Legion logo did appear. But pressing F2 repeatedly on my external keyboard still did not boot me into the BIOS utility...

2025-09-09 Wk 37 Tue - 12:44 +03:00

So I guess eventually this does work, not sure why it takes a while.

But the issue is our USB drive is not recognized with the Windows 11 OS we burnt into it now...

Spawn [001 Windows 11 USB Drive burnt with dd not recognized by Lenovo Laptop](001%20Windows%2011%20USB%20Drive%20burnt%20with%20dd%20not%20recognized%20by%20Lenovo%20Laptop.md) <a name="spawn-issue-0b45b0" />^spawn-issue-0b45b0
