---
layout: post
title: Synaptics Pointing Device app stops working randomly
date: 2017-02-02 16:16:18.000000000 +07:00
tags: windows lenovo
---
(Thread: http://superuser.com/questions/504178/synaptics-touchpad-stops-working-randomly)

This is very furstrating as you know I switched to use Windows machine. As long as the synaptics pointing device app works my experience with this inferior touchpad will not be too bad. 

However, this app, that enables all the great features of multi-touch, crashes quite randomly and frequently. I could not bear to restart my machine because of this problem alone.

Finally, I found, at least, a way to restart it without restarting my machine.

I tried to do the process kill, curiously, even the touchpad's multi-touch ability has gone .... there still is the synaptic process runnnig, obviosuly, with no effect.

```
taskkill -f -im syntpenh.exe
```

And, then I just rerun this many times, found out that the process is kept restarting as it's killed, and the multi-touch capabilities have come back. 

So, if the problem arises again I will just kill the process and wait for it to restart.
