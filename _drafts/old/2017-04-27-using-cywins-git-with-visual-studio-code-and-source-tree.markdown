---
layout: post
title: Using Cywin's Git with Visual Studio Code and Source Tree
---
You installed the cygwin's git and hope it to work... but nah .. it didn't with Visual Studio Code, and even with Soruce Tree you still need some guidelines.

### with Source Tree 

this is the easier part, you just have to "point" which execute table of git you want to use. And if you are like me, installing cygwin from chocolatey, your git executable should reside at "C:/tools/cygwin/bin/git.exe". Thus, it should be the path for source tree and that's all !

Note: even you can simply ignore this configuration by using the embedded version of git instead, but it won't "work well" with the cygwin's git anyway .. I think it's a matter of conflicting "paths". All in all, just you the same git for both !

### with Visual Studio Code

It's kinda tricky becasue it's not officially supported, as you might see from this thread yourself https://github.com/Microsoft/vscode/issues/7998, they don't really support cygwin. But, there is a way to make it work as well, say more a workaround. 

You can create a folder link at `c:/cygdrive/c -> c:`, to make the path query `/cycdrive/c/...` work, as jastorgalj has pointed out ... 

> **jastorgalj** commented on Sep 8, 2016

> Solution for me (Windows 10 - git from cygwin)

> create dir - mkdir c:\cygdrive
open a cmd and goto - cd c:\cygdrive
créate link to c:\ - mklink /j "c" c:\
and ready..
