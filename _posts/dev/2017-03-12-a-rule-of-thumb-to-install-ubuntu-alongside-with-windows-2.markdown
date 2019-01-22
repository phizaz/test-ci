---
layout: post
title: A Rule of thumb to Install Ubuntu Alongside with Windows
date: 2017-03-12 07:09:36.000000000 +07:00
tags: ubuntu windows
---
***A rule of thumb is: if you install windows in UEFI mode (which Windows 10 is almost always be), you will need to install Ubuntu in UEFI mode as well.***

If you missed that out prepare to have problems with booting. This kind of problem won't be fixed with **ANY** tools regrading the booting problems, maybe **any** is an overstatement, but I did try the **boot-repair**  (https://help.ubuntu.com/community/Boot-Repair), all it's saying was boot it with UEFI mode. 

#### So, how to install Ubuntu with UEFI mode then ? 

As the link suggests, you can make sure that you do it right if you create an only UEFI bootable usb. This way you can guarantee that if it works it's gonna work right.

The process of creating a UEFI bootable usb is in the link, but I will recap it here:

1. Insert USB.
2. Format it to FAT32, flag it with `boot` and `lsb`, you might use the **Gparted** to do this.
3. Mount the USB.
4. Copy all files from the **.iso** file, to the USB, including all hidden files (**ctrl + h**, in nautilus to see the hidden files)
5. But, I prefer this way better, `7z x <.iso_file> -o<usb_mount_path_with_trailing_slash>`
6. That's all.

Tried to boot it, you might have to set your bios to enable UEFI mode or even **prefer** it first.

#### If you already have problem

You still can make it boot up, but only with the help of **Super Grub2 Disk** (http://www.supergrubdisk.org/super-grub2-disk/), it will automatically detect all installed bootable OSes in all your drives, you can select one of the option and boot into your os. It's not permanent at least you can boot into. 

If you want to reinstall grub2, you can try `sudo grub-install /dev/<yourdrive>`, you must state the device name not the partition name. In my case, it didn't work because I installed the Ubuntu in legacy mode, and trying to boot it alongside with UEFI Windows 10, it just won't work.
