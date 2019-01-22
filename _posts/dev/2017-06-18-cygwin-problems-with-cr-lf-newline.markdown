---
layout: post
title: Cygwin Problems with CR/LF newline
date: 2017-06-18 07:29:50.000000000 +07:00
categories: dev
tags: cygwin
---
Upon finding this short post, you might have encountered the notoriously newline issue in Windows. The more you try to be UNIX-like on Windows the more the issue pronounces itself.

For a prominent example, when you are trying to build some certain program on Windows. You will more than likely to encounter the excess `\r` problem (or in some circumstances will you see `^M`). I am not certainly sure how it happens but it is sure that any native Linux program which we runs on Windows will struggle to understand `\r` and must fail.

So this is where `dos2unix` and `unix2dos` commands come in to play. It mitigates the problem if your source of problem comes from the file system itself. 

But, it is not the only source of problem.

Some commands just output with a newline and it might have problems, for example:

```
jupyter --data-dir
```

Which should output the `data-dir` for `jupyter` in this case it might be `C:\Users\<user>\AppData\Roaming\jupyter`, but in practices, it is not, it has a newline, and not only `\n` conventional newline. It comes with `\r\n` newline. which becomes a problem when you trying to substitute this command in another command like:

```
$(jupyter --data-dir)/abc
```

You will not get `C:\Users\<user>\AppData\Roaming\jupyter/abc` as you might have expected. You will rather get `C:\Users\<user>\AppData\Roaming\jupyter^M/abc`. This breaks the command !

As you can see, it is really a problem and cannot be solved by simply running `dos2unix` because it is not a file.

Fortunately, there is a way to make `bash` completely ignore `\r` character. It only works on bash. 

In `.bash_profile`

```
# ignore carriage return
export SHELLOPTS
set -o igncr
```

And then source it `source .bash_profile`.

Now, all the excess `\r` will be ignored. And now you will be happy :D.

The only question persists so far is that: Is there something like this for `zsh`, which is arguably much superior to `bash` ? which I have yet to find.


