---
spawn: '[[000 Laptop just gives black screen when trying to install OS via USB]]'
status: todo
---

\#boot #windows #format

# 1 Objective

I installed a Windows 11 update, and suddenly came back to find that my laptop no longer boots. It seems we're going to have to install windows 11 again on it.

# 2 Journal

2025-09-08 Wk 37 Mon - 23:53 +03:00

We can find the Windows 11 ISO in [microsoft windows11](https://www.microsoft.com/en-us/software-download/windows11).

On windows, we usually use [rufus](https://rufus.ie/en/) to burn images. But right now we're on Ubuntu Linux.

This [howtogeek post](https://www.howtogeek.com/414574/how-to-burn-an-iso-file-to-a-usb-drive-in-linux/) recommends

````sh
sudo dd bs=4M if={iso_file} of=/dev/{dev} conv=fdatasync status=progress
````

We can discover the device of interest `{dev}` using `lsblk`.

In my case, this is a USB drive so it's found under `/media/{user}/`, but we're interested in the device code in the first column of the `lsblk` output.

2025-09-09 Wk 37 Tue - 00:19 +03:00

It stopped reporting after like 2 logs and a part of a log for some reason, but it did eventually finish.

The USB drive adopted a Windows prefix name,  `CCCOMA_X64FRE_EN-US_DV9`.

And it says it uses the [UDF](https://en.wikipedia.org/wiki/Universal_Disk_Format) file format.

Windows 11 uses 5.8 GB of storage.

2025-09-09 Wk 37 Tue - 00:33 +03:00

Spawn [000 Laptop just gives black screen when trying to install OS via USB](issues/000%20Laptop%20just%20gives%20black%20screen%20when%20trying%20to%20install%20OS%20via%20USB.md) <a name="spawn-issue-4d3bdf" />^spawn-issue-4d3bdf

# 3 Index

## 3.1 Entries

## 3.2 HowTos

## 3.3 Ideas

## 3.4 Inferences

## 3.5 Investigations

## 3.6 Issues

[000 Laptop just gives black screen when trying to install OS via USB](issues/000%20Laptop%20just%20gives%20black%20screen%20when%20trying%20to%20install%20OS%20via%20USB.md)

**Pend**
[001 Windows 11 USB Drive burnt with dd not recognized by Lenovo Laptop](issues/001%20Windows%2011%20USB%20Drive%20burnt%20with%20dd%20not%20recognized%20by%20Lenovo%20Laptop.md)

## 3.7 Tasks
