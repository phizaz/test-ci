---
layout: post
title: Install and Enable Zram on ubuntu
---
(thread: https://www.solaris-cookbook.eu/linux/ubuntu-zram-memory-compression-compressed-swap-ram/)

```
sudo apt-get install zram-config
```
And thene reboot.

**zram** should pretty much act like a swap partition, let's see:

```
cat /proc/swaps

Filename				Type		Size	Used	Priority
/dev/zram0                              partition	1008092	0	5
/dev/zram1                              partition	1008092	0	5
/dev/zram2                              partition	1008092	0	5
/dev/zram3                              partition	1008092	0	5
/var/lib/swapspace/1                    file		1348320	0	-1
/var/lib/swapspace/4                    file		1774204	0	-2
/var/lib/swapspace/2                    file		1415992	0	-3
/var/lib/swapspace/3                    file		1583456	0	-4
```
