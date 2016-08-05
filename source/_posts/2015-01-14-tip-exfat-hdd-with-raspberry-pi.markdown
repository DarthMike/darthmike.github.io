---
layout: post
title: "Tip: ExFat HDD with Raspberry Pi"
date: 2015-01-14 10:11:11 +0000
comments: true
categories: [raspberry pi, tips]
---

A few days ago I switched my HDD on my Raspberry Pi to a SSD drive. I use the Pi as a NAS serving files at home and I wanted to use a HDD for music with less storage but more reliability. This drive runs 24/7. I also chose exFat for the filesystem. I write this as a future self-reference, but may be useful for somebody else, so here it goes.

<!-- more -->

Steps:
## Find the drive
First you need to find the drive you want to mount. Run:
```bash
sudo fdisk -l
```

Which will list disk partitions in the system. In my case the interesting bit is:
```bash
Disk /dev/sda: 240.1 GB, 240057409024 bytes
```
And this one:

```bash
Disk identifier: 0xb6474e4f

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1               2   468862126   234431062+   7  HPFS/NTFS/exFAT
```

So note the location of the partition - in my example **/dev/sda1**.

##Mount it manually
Test that you can mount it with *mnt* command:
```bash
mkdir /mnt/PIHDD
mnt /dev/sda1 /mnt/PIHDD
```
If you then check the contents they should be visible:
```bash
ls /mnt/PIHDD
…
```

##ExFat
If you have an exFat drive, mounting will not work, as you need a driver with support for it. On Debian and Raspbian, just install it with the package manager:
```bash
sudo apt-get install exfat-fuse
```
##Mount it automatically
So you can mount and unmount the USB drive manually. But you can automate it even further so every time you start the Pi, it mounts the drive to the specified location. For this you need the UUID, not just the location, as it could potentially change.
```bash
sudo blkid
```
Gives you a list of devices and their UUIDs. Note the one for your partition:
```bash
/dev/mmcblk0p1: SEC_TYPE="msdos" LABEL="boot" UUID="787C-2FD4" TYPE="vfat"
/dev/mmcblk0p2: UUID="3d81d9e2-7d1b-4015-8c2c-29ec0875f762" TYPE="ext4"
/dev/sda1: LABEL="Music" UUID="54AF-0252" TYPE="exfat"
```
Then last step is to modify the file in /ect/fstab to mount your  partition:
```bash
UUID=54AF-0252 /mnt/PIHDD exfat defaults,auto,umask=000,users,rw 0 0
```

For the meaning of each value run the manual pages, but these are reasonable defaults for home drives.

Now every time the Pi starts it knows how to mount that drive. Also, if you run *sudo mount -a* command, it will also know how to mount it. Good for quick system maintenance.

**EDIT 05/08/2016**:
I'm seen a lot of traffic coming to this post, so I wanted to give a heads up to anybody reading this:
**I'm no longer using ExFat for my Raspberry Pi**. The reasons are in [this post][new-reference].
If you want to try it yourself, you're very welcome, and the steps are still valid AFAIK.

[new-reference]: {{site.url}}blog/2015/04/14/exfat-with-raspberry-pi-continued/
