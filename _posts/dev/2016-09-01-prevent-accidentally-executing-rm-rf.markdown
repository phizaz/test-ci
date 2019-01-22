---
layout: post
title: Prevent accidentally executing  rm -rf /*
date: 2016-09-01 04:19:26.000000000 +07:00
categories: dev
tags: ubuntu
---
It happened to me! 
I don't want so to you!

Let's prevent!

Using `safe-rm`

On ubuntu:

```
apt-get install safe-rm
```

You can see the `safe-rm` is in `/usr/bin/`

```
which safe-rm
```

As the accident is too grave for us to take any risk, I did `move` the old `rm`

```
mv /usr/bin/rm /usr/bin/rm-old
```

And create a symlink for `safe-rm`

```
ln -s /usr/bin/safe-rm /usr/bin/rm
```

Note that It can have some side effects, but be on the safe side... If anything should go wrong because of moving the `rm` out of place you can simply put it back for the moment.

You can edit the safe list at `/etc/safe-rm.conf` adding your home directory and other precious directories of your choice

`safe-rm` can also be easily installed on OSX via homebrew

```
brew install safe-rm
```

You can config it by creating the file `safe-rm.conf` at `/etc` not `/usr/local/etc`

Finally, (you might need to re-login the shell) removing unwanted directories is now prevented!

Try (at your own risk) : 

```
rm -rf /etc
```
