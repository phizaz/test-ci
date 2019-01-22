---
layout: post
title: Windows 10 Mouse Pointer Stutters under High CPU Load
date: 2017-02-18 08:10:03.000000000 +07:00
categories: dev
tags: windows
---
When I move my mouse while the CPU is under a high load, the mouse pointer occasionally *stutters*, lags, and after a short peroid of time it became as normal, it suffers more when I tab `alt+tab` to switch windows. 

It's super annoying that even I have the latest CPU to offer from intel, the Kaby Lake, I still have this problem under a high cpu load.

Intel is not blame, of course, it's rather Microsoft, who manages the processes.

I serached for the rememdy, about which I am not even sure that they suffered the same problem as I do:

https://www.tenforums.com/drivers-hardware/14107-windows-10-mouse-lagging.html
https://www.sevenforums.com/hardware-devices/212054-mouse-stutter-lag-when-processes-using-high-cpu.html
https://forums.anandtech.com/threads/windows-10-and-mouse-lag-stutter-when-cpu-usage-is-high.2444858/

To the end of conversation, the most common was **let the microsoft fix this** in the meanwhile reinstatll a fresh copy of the Windows 10, as many have done and the problem gone. That's not good enough for me I am not satisfied by that. I don't want to clean it up and start anew that's not even called fixing!

The best shot I got so far was the cpu optimization program suggested in some of the threads, Process Lasso (https://bitsum.com/), it's not free by the way, I'm using the free limited-feature version of it. I'm trying it out to see if it makes any difference, so far I think my system is **a bit** less susceptible to the problem, but I can still feel it, at least on the system startup. 

So, in conclusion, there is no remedy for now, Process Lasso or others of its kind **MIGHT** help but not actually make it gone.
