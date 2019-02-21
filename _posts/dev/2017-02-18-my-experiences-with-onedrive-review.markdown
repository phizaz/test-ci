---
layout: post
title: My Experiences with OneDrive (Review)
date: 2017-02-18 09:45:55.000000000 +07:00
categories: dev
tags: cloud
---
I have been using OneDrive for 2 months already, here I like to review my experiences with **OneDrive** a bit. In short, it's **sluggish, memory hungry, and not FAULT tolerant.**

#### It's Sluggish and memory hungry

Maybe, I exaggerated on this because the demand I put on it is quite extraorditary. I actually put **300k files** (small ones by many) into the OneDrive system. It becomes **processing changes** forever. It took **days** (2 days + or -, if I counted correctly) to finish. And yet most of the time, not the internet bandwidth that it consumes, it rather the CPU itself that always spiked, with the memory usage of about **1 GB** all the time.

Even I use the latest CPU from intel, admittedly it's ultralow voltage series, but hey its the latest Kaby Lake ! I expected much more from it rather than be totally consumed by the simple process of syncing!

***Work harder Microsoft!***

#### It's not fault tolerant

First thing first, if you're moving from the unix-world filesystems like `ext4` or `hfs+`. You would very likely encounter the **path too long** problem in `NTFS`. **NTFS** supports only a puny fraction of maximum path length of those filesystems. Syncing these files to the Windows machine is a disaster! You might have to resolve it by hand, moving folders after folderes to the shorter paths. It was just a pain, but not necessarily contributed to the OneDrive, but anyways I will put it here!

It's not the end, there are many more scenarios in which I encounter problems. When I changing the contents in the drives in SOME WAYS it made the **OneDrive** process crashed. I reckon the moment to be when I used the program `rsync` to sync files from a remote host to my machine, it crashed severely and cannot be easily solved by just restarting the program. I have to make sure than everything `rsync` done to the folder has been undone. And **relink** the account back, of course waiting all the initial file checkings again, many days have to be spent.


#### So, why bother using it ?

In short, **it's the cheapest**, you will be amazed by how cheap it is compared to the competitors (with 1TB of drive space). You have to take into the account that with OneDrive subscriptions **also come with the Office 2016 subscription**. Subtracting the already cheap subscription of the OneDrive, this makes it even cheaper.

But, at the end of the day, if you're looking for better syncing technologies, don't even bother with OneDrive, just not yet. **Dropbox** is much a better choice, I did try, I can say that by *faaaaaar*. **Google Drive** is not much better than **OneDrive** if it is at all. **Amazon Drive** don't bother with it, it doesn't work, its syncing feature just came the last version do not expect it to be ready to use yet. 
