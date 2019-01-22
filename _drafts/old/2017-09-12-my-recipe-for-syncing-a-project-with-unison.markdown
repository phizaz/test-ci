---
layout: post
title: My recipe for syncing a project with "unison"
---
The general idea is like having a Dropbox client installed on a computer A and a computer B, and have files on these two in sync.

We don't need Dropbox to achieve this though ... you only need "unison".

For a proper introduction, you might want to go to the original website, https://www.cis.upenn.edu/~bcpierce/unison/.

And here is the manual: http://www.cis.upenn.edu/~bcpierce/unison/download/releases/stable/unison-manual.html.

Actually, with a careful read and a reasonable time for trial and error you can use this effectively. 

But I have tried it for my project and here is the gist for you, assuming that you want to have a synced project on machine A and B, and only these twos.

### Installation

It has a compiled version for Windows you just need to grab it and put it in a directory within your `$PATH`.

Try `unison --help` checking if it is installed properly.

### Basic Usage and Options

Unison has the idea of "root" which is the terminal for each sync, A and B in this case are roots. 

Unison has the idea of configuration file. It is kept in the `~/.unison` directory with application specific files, which you should not tamper with. In this directory, you can create a config file for your project, for example

```

```

### My Config File

### Actual Use

#### Compression + Unison

### Cautions

1. Don't remove the whole project directory. 
2. If anything goes wrong, use **rsync** to make both copies the same this will eliminate most of the problems of "out of sync". `rysnc -a <A> <B>` will do, or with `-z` option if you want to sync with compression.
3. Running with "repeat" option can lead to CPU leak, at least it happens on Windows versions of unison. Use `while true; do unison; sleep <time>; done` instead, it is less optimized, but it will never go wrong.
