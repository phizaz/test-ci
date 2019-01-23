---
layout: post
title: Ubuntu 16.04  with Nvidia GTX1060 Sound Doesn't Work
date: 2016-09-03 06:23:11.000000000 +07:00
categories: me
tags: ubuntu
---
Even after properly installed Nvidia graphic card driver (367.44, latest) still the sound doesn't work.

My setting is **HMDI through GTX1060** connecting to a TV.

It takes me like 4 hours to find the solution (trying on many solutions).

People have discussed sound problem on 14.04 http://askubuntu.com/questions/512621/unable-to-get-audio-through-hdmi-connection-to-tv-with-ubuntu-14-04 They said it might be the permission problem, so I did adding ${USER} to the group, but actually I was already there!

I tested buzzing sounds to a designated port, as mentioned in http://askubuntu.com/questions/112512/ubuntu-refuses-to-output-audio-via-hdmi. Sure enough, it worked, I could here the buzz sound perfectly with the proper audio device listed in **aplay -l**.

So, the device is there and working, but somehow it is not listed in the ubuntu sound devices. It turns out that there is a *way around* that gets me to the solution as well. I happened to run **pavucontrol**, and a small window popped up. After playing around, I selected **HDA Nvidia Digital Stereo (HDMI 2)** as a *fallback* device, now it worked!

I suspect that this still might not be the *right way* that really gets to the point, but at least it works and I'm so tired of this puny problem already! :D

Update: I think I have found a reasonable solution! It's in the **pavucontrol** I've mentioned. Go to **Configuration** menu in it. You'll see something like **Built-in Audio**. This one doesn't work, just turn if off. Now go see the **Output Devices** menu. You should see there is only **HDA Nvidia Digital Stereo** now, which is great, I think you should now hear the sound already.
categories: dev
