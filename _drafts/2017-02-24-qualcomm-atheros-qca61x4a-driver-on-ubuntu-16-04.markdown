---
layout: post
title: Qualcomm Atheros QCA61x4A Driver on Ubuntu 16.04
---
https://github.com/kvalo/ath10k-firmware

From: https://ubuntuforums.org/showthread.php?t=2323812
```
sudo apt-get install git
git clone https://github.com/jeremyb31/ath10k-firmware.git
sudo cp ath10k-firmware/ /lib/firmware/ath10k/
```

From: http://askubuntu.com/questions/763080/no-wifi-in-qualcom-atheros-ubuntu-16-04-acer-aspire-e-15

```
sudo apt-get update
sudo apt-get install git
git clone https://github.com/kvalo/ath10k-firmware.git
sudo mkdir /lib/firmware/ath10k/QCA9377
sudo mkdir /lib/firmware/ath10k/QCA9377/hw1.0
cd ath10k-firmware/QCA9377/hw1.0
sudo cp *  /lib/firmware/ath10k/QCA9377/hw1.0
cd /lib/firmware/ath10k/QCA9377/hw1.0
sudo mv firmware-5.bin_WLAN.TF.1.0-00267-1  firmware-5.bin
```

From: http://askubuntu.com/questions/661424/ubuntu-14-04-wireless-not-working-no-network-interface-atheros-168c003e-dev

> You can get it work in Ubuntu 14.04 by installing kernel 4.2 and the updated firmware. Run in terminal:

```
sudo apt-get install linux-generic-lts-wily
wget https://mirrors.kernel.org/ubuntu/pool/main/l/linux-firmware/linux-firmware_1.157_all.deb
sudo dpkg -i linux-firmware_1.157_all.deb
```
> In Ubuntu 15.10 and later you only need to install the firmware:
```
sudo apt-get install linux-firmware
```

