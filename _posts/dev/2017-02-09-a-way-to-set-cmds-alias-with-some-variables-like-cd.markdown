---
layout: post
title: A way to set cmd's alias with variables like %cd% and %home%
date: 2017-02-09 16:50:16.000000000 +07:00
categories: dev
tags: windows
---
To set alias for a very long command in cmd, just type 

```
alias name=command
```

And you're done. 

However, one thing that would be nice to have is that if we have a command that is not so **static** meaning we want the ability to use, I don't know the proper name, **variables** like %cd% to substitute for the current directory location of the caller.

It's important to insert %cd% in a plain string into the alias, and not to be resolved to get the current directory location for that time, which renders the command static again.

Say, if I want to add this command, `echo %cd%` to the alias. I would rather get `echo \my\current\location\at\the\time\i\invoke\alias\instead` It's not so good right ?

Now, I try to find a way to escape the % (percent-sign) from the command line, which some say to be `^`. With `^` you can `echo ^%cd^%` and you get `echo %cd%` in your standard output, which should be about right ....

Try using this trick with alias .. I found that it doesn't work. To me, it's like alias has gone another step ahead of me. No matter how I try to escape the % sign, alias always know how to de-escape it and evaluate the expression I always wanted to hide.

#### Solution: Write a proxy program (runner)

I reasoned with myself if I still want to use `alias` to do the job. I have to use the strings it doesn't recognize ... may be `${cd}` insead of `%cd%` and `${home}` instead of `%home%`.

Start with a string replacing progrem ... I'll try to use only **batch script**. Since, I can access to the arguments via `%*` in batch script and replacing with the syntax `!str:pattern=replacement!`. I can now replace all the arguments with the correct one

```
setlocal ENABLEDELAYEDEXPANSION
set str=%*
set str=!str:${cd}=%CD%!
set str=!str:${home}=%HOME%!
```

Note: With no particular knowledge about `ENABLEDELAYEDEXPANSION`, I was suggested to put it there.

Now, I can suspect the variable `str` to be the replaced arguments ready to be supplied to the program. The only missing part is only the execution part, which is as easy as `%str%`

Here is the full code: 

```
@echo off

REM run command by parsing ${cd} to %cd% and ${home} to %home%
setlocal ENABLEDELAYEDEXPANSION
set str=%*
set str=!str:${cd}=%CD%!
set str=!str:${home}=%HOME%!

REM run the parsed command
%str%
```

Let's name it `run.cmd` and save it into some directory within the `PATH` environment variable.

If I were to execute a command I would rather use the `run.cmd` as a proxy instead:

```
run.cmd echo ${cd}
```

It equals to :

```
echo %cd%
```

But, the whole thing is for this moment when we can do this to `alias`:

```
alias whereami=run echo ${cd}
```

And you can practically use:

```
whereami
```

And gets your current directory location everytime not the one at the time when you added it.
