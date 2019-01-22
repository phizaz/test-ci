---
layout: post
title: Python's pseudo terminal (PTY) examples
date: 2016-09-03 15:36:33.000000000 +07:00
tags: python
---
If you're like me, when you first starting using `subprocess.Popen` you would inherently think that you can invoke any kind of command using this.

**But, No!** it won't `subprocess.Popen` doesn't allocate a pseudo terminal during its run. That means some command *might not run*. To name one, it is `ssh -t` (with pseudo terminal allocated) it just won't run and will raise an exception.

Some others still run without a pseudo terminal, however it might not really like what you have seen using it under the **Terminal.app** (or others of that kind). For example, `docker pull` command supports both with and without terminal. But its output's gonna be different. If you can recall, the *normal* output of it has progress bars of some sort, this is not the case for running `docker pull` without pseudo terminal.

This simple fact, however, took me days to figure out. I was trying to **duplicate** the `stdout` of `subprocess.Popen` at the time. Regardless of my efforts, I cannot see the progress bars of the `docker pull` command (I just read output from the `subprocess.PIPE` and put it into many ends). That astonished me greatly. 

I wasn't sure at the time whether I did it right or not. Maybe control characters (in order to create progress bars you need these guys) just won't go through the pipe. It turned out that was bullshit, every control characters are represented in bytes and **WILL** go through the pipe if there is any.

Finally, I found that it is the `docker` it self that changes the output pattern when it finds that it's not being run with a pseudo terminal. And yes, my puny pipe is not a kind of pseudo terminal, so progress bars are not in the output.

#### A new approach

I restarted myself in knowing this. I intended to attach a pty (pseudo terminal) onto a command of my choice, including `docker`

It's not trivial though, at least for me, to understand how to use **Python's PTY** library. I just didn't have the knowledge of how pty would take part in the whole process.

A lot of googling had been done, I read a blog (http://sqizit.bartletts.id.au/2011/02/14/pseudo-terminals-in-python/) I think I owe the writer to some extent. After gazing at the code, I finally grasped its algorithm, the pty's place in the code, and how does it interact with the rest.

General way of using Python's PTY is something like this:

1. use `fork` to create another process that is attached with pseudo terminal (so it can run those exceptional commands), called this a child process. 

2. at the `child` process, we will just execute a command of our choice in the way that can be a bit destructive like `os.execlp(..)` this kind of statement will terminate itself from being python to the designated process completely (and never to return).

3. at the `master`, the master will know the child's pid, and child's stdin file descriptor (fd). The latter is extremely helpful it's a pipe of bi-direction. **Writing from it is the same as writing to the child's stdin, and reading from it is the same as reading from the child's stdout**.

If you want, as I do, to read child's stdout and duplicate this, one, to the master's stdout and, second, to a variable. Your algorithm will be something like this:

1. use `select.select` command to watch a pipe of child's stdin fd.

2. if there is anything output from the child, we will read it from the pipe.

3. prints it through master's stdout, and also writes it to a variable of your choice.

And thats's how you do it!

#### One more thing

The approach I just mentioned including the website I posted doesn't show how to get the **exitcode** after the command has run. Actually, I found no way to get it using the current knowledge I have. So, I yield to a more robust library like `ptyprocess`, I think you should consider using it because the developer has put so much effort to make it as platform independent as possible.

You can have a sip to my implementation here:

<script src="https://gist.github.com/phizaz/c161879e6996b0d05edce16dd6b2809f.js"></script>
