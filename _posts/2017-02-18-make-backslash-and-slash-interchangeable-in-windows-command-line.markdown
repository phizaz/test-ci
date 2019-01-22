---
layout: post
title: Make \ (backslash) and / (slash) interchangeable in Windows command line
date: 2017-02-18 09:16:37.000000000 +07:00
tags: windows
---
File: `slash.cmd`

```
@echo off
setlocal ENABLEDELAYEDEXPANSION
set str=%*
set str=!str:\=/!

REM run the parsed command
%str%
```

It will execute the command while replace all the `\` to `/`. Like, `docker run -it -v %HOME%\test\dir:/workdir debian bash` to be `docker run -it -v %HOME%/test/dir:/workdir`. Since, the Windows command prompt will only auto-complete the path using `\` which doesn't work with Docker (linux) paths.
