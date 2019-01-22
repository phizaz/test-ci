---
layout: post
title: Cygwin + Choco + SSH + Git Recipe
---
1. install `choco` (**chocolatey**)
2. install **cmder** (via `choco install cmder` you also need administration privilege for installation)
3. install **cygwin** (via `choco install cygwin`)
4. install **cyg-get** (via `choco install cyg-get`), please note that `cyg-get` cannot be executed using cygwin's `bash` because it's a `.bat` file.
5. install `cyg-get openssh git wget curl zsh`
6. instal **"oh-my-zsh"** using the code snippet they provide
7. configure your **cmder** to use "zsh.exe"
8. if you have visual studio code, use "c:/tools/cygwin/bin/zsh.exe".
9. to make **ssh** works as expected you have to set default **home** directory in "/etc/nsswitch.conf" file as follows

```
# add this line
db_home: /cygdrive/c/Users/%U
```

Note that, without which, ssh will look for the path `.ssh` in the wrong place.

**You'll likely to have the problem with $(... | ...)** (piping

To make the git work with your existing Visual Studio Code and Source Tree you also need to do something more ... 
