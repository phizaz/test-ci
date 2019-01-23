---
layout: post
title: Migration from Mac to PC
date: 2017-01-06 16:26:24.000000000 +07:00
categories: dev
tags: windows mac
---
**After a month of migration ... I've made quite a difference so I'll write a new article for it.**

# Deprecated !

Many of this is based on the post [https://github.com/felixrieseberg/windows-development-environment](https://github.com/felixrieseberg/windows-development-environment)

As of 2018, I do not anymore keep mac conventions (be it shortcuts etc.), I have relearned all the keymaps and use it as frequent as the old days with my mac. A lessen learned was that I should not customize to much such that my machine is so different from other machines out there in the world. I was at some point crippled while using other people's machines. 

### Shells

You know before hand that you would miss the shell when you become to use Windows. This is the first thought I had when thinking of migrating. When I really made the switch, it turned out not to be that bad ... since someone are just so awesome to create `cmder` to replace the inferior `cmd.exe`, and also Windows team is currently working hard, I guess, to bring full support for Ubuntu bash to Windows, of that, I would love to see soon. 

Now, I use `cmder` as a shell with `choco` (Chocolatey) as a package manager (like apt-get or brew), also I try Ubuntu bash on Windows and still looking for a way to combine these things together with the right balance.

#### Git
Since I chose to `choco`, this is a piece of cake ! 

```
(cmder with administrator privilege)
cinst git.install
cinst poshgit

# Restart PowerShell / CMDer before moving on - or run
$env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") + ";" + [System.Environment]::GetEnvironmentVariable("Path","User") 

cinst Git-Credential-Manager-for-Windows
```

Note: Copied from the post mentioned above.

#### ssh-copy-id

It is not a big deal though, but I really love this tiny program. There is no easy way to make it available in Windows. I just can find some Python scripts to do it ... so just put it in some easy access folder and python it every time I want to use it, which is not so bad ... 

### Keyboard Shortcuts

#### Language Switching 

I use both Dvorak and Thai (Kedmanee) thus I have to switch between them. Using default Windows settings I would have to use `alt + shift`. While trying to change this to some other alternatives closer to the old of macOS, I didn't know that `win + space` is the DEFAULT key sequence for changing input language. Knowing this gave me joy because it is really familiar for me consider that in Macs we use `super + space` to change input languages.

#### Visual Studio Code

I made a key mapping from `ctrl` based shortcuts to `alt` based shortcuts basically because it is closer to the macOS, and also, if you use Vim, `ctrl` based shortcuts are quite annoying because there are some Vim command using `ctrl` and causing interference. If you like, this is [the script](https://gist.github.com/phizaz/9b47fc936931ee69a817c6dd504d12d0) I use.

#### Tap Switching

I love the way that I can switch to the next and previous tab using `super + shift + ]` and `super + shift + [` in my old mac, I want to keeping doing it now while I'm using Windows.

This is done by **AutoHotKey**. It is an awesome piece of software. You can write script, and thus it is fully customizable. I did some trial-error and found this code worked just fine:

```
+![::
    Send, ^+{TAB}
    return
+!]::
    Send, ^{TAB}
    return
```

The most frustrating part was the `return` part. I didn't know that the script works like `switch() { case ... }` syntax, it will just fall through if not encountered a `break` or `return`.

Update: I updated many more key-mapping if you find it interesting you can get it [here](https://gist.github.com/phizaz/031e6620568b3aa0fc8ae801711cc0b6)
categories: dev

Also, don't forget to set it run automatically after boot. And If you're like me you don't know where to find the `startup` folder anymore (in Windows 10). Just type this in `run.exe` :

```
shell:startup
```

Now, put the **compiled script** in to it. You are good to go!

Update: I've found starting up program in this way is quite slow (may be with low priority), using **Task Scheduler** is way faster !
categories: dev

### Touch Pad Problems

I must admit that in terms of touch pad others manufacturers are way behind Apple. Apple doesn't need disabling the touch pad while typing yet keep the pointer idle perfectly. 

Also, while clicking you can feel that mac sure does a way better job. 

I note the steps I did to make me more familiar with the new machine of Windows.

#### Pointer Acceleration Problem 

At the first touch, I could feel the awkward difference of the pointer acceleration between macs and PCs (using Windows). It is that while using mac my pointer moves quite constant proportionally to the distance I move my finger, and that makes navigating quite accurate. Windows has the "Enhanced Precision" which is a good thing while using a mouse but not the case for touch pad. I have had this disabled. 

#### Touch Pad Disability While Typing

Moving the mouse pointer while typing ? It's here to solve the problem for you... but it comes with a cost ... for me an unbearable cost! I frequently use my touch pad while typing or at least holding a key in the keyboard. The program prevents me this and it's not acceptable. 

In my case, Lenovo 710s, Just go to the Synaptics software and disable the "Palm Tracking" to allow touch pad to be working during your typing.

### Keyboard

#### F1, F2, F3 

I love to keep F{n}, where 1 <= n <= 12, the way it used to be. A way to do this may vary by your machine. In my Lenovo 710s, I set it in an app called **Lenovo Companion** in the **Settings** section.
