---
layout: post
title: "exFat with Raspberry Pi (continued)"
date: 2015-04-14 22:35:50 +0100
comments: true
categories: [raspberry pi, tips]
---


I [wrote]({{site.url}}/blog/2015/01/14/tip-exfat-hdd-with-raspberry-pi/) a while ago a tip, mostly for my future reference, how to set up a HDD with Raspberry Pi formatted using [exFat](https://en.wikipedia.org/wiki/ExFAT). After more intensive testing on my Samba setup, I've decided to go back to NTFS as my format of choice.

<!-- more -->

I chose *exFat* over *NTFS* because at home I have 2 main OSes I use: Windows and OSX. My Windows computer is for historical reasons still used, mainly for gaming, but also for other family members. I have a Macbook as the main tool, so the choices for a common file system are a bit limited: *Fat32*, *NTFS*, or *exFat* as far as I know.

I previously used *NTFS* on my Raspberry Pi external USB HDD, but I wanted to try *exFat* and assess the performance; hence that previous [post]({{site.url}}/blog/2015/01/14/tip-exfat-hdd-with-raspberry-pi/).

So for anybody reading this, as of today I don't recommend *exFat* for your NAS setup. I've found the [driver implementation](https://packages.debian.org/unstable/main/exfat-fuse) to be unstable - it **is** actually labelled as unstable, to the point of having the disk disconnected for no apparent reason, and being forced to restart the Pi.

Let's see if the driver improves in the future.

### EDIT 29/10/2016: Raspbian updated
[@tienbuiDE][tienbuiDE] recently [told me][tweet] that the installed package with newer Raspbian versions has been marked as [stable][jessie_package]. I think this is due to Raspbian being based on newer Debian Jessie (not Wheezy). So it's a good time to test the drive with exFat again!. So please do your tests and see if it works for you. I'll report when I do mine.

[tienbuiDE]: https://twitter.com/tienbuiDE
[tweet]: https://twitter.com/tienbuide/status/789170927061786625
[jessie_package]: https://packages.debian.org/de/jessie/exfat-fuse
