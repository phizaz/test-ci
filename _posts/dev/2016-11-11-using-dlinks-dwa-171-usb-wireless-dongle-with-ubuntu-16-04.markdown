---
layout: post
title: Using DLink's DWA 171 USB Wireless Dongle with Ubuntu 16.04
date: 2016-11-11 19:21:05.000000000 +07:00
categories: dev
tags: ubuntu
---
It doesn't come with a working official driver though.

So, I googled for it, and found: https://ubuntuforums.org/showthread.php?t=2168426

Here is an excerpt worth mentioning, from user **chili555**

```
sudo apt-get install git
git clone https://github.com/abperiasamy/rtl8812AU_8821AU_linux.git
cd rtl8812AU_8821AU_linux
# make with 4 concurrent jobs
make -j4
sudo make install
sudo modprobe -a rtl8812au
```

After reboot, your device will discover for wireless networks.

It's important to note that after system updates, you'll need to recompile the driver and install it again. Simply, restart the whole process except the cloning one.
categories: dev
