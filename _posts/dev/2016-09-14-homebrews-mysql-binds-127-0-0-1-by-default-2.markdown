---
layout: post
title: Homebrew's MySQL binds 127.0.0.1 by default
date: 2016-09-14 02:46:06.000000000 +07:00
categories: dev
tags: mysql
---
I tried configuring `bind-address` option in `my.cnf` many attempts, but found no success.

After running `ps -ax | grep mysql` it shows:
```
83783 ??         0:00.03 /bin/sh /usr/local/opt/mysql/bin/mysqld_safe --bind-address=127.0.0.1 --datadir=/usr/local/var/mysql
```

It's kinda like `mysqld` is bound to run as `127.0.0.1` regardless of the `my.cnf`

#### A Post on DBA.stackexchange

*http://dba.stackexchange.com/questions/55958/cant-remote-access-mysql-server-running-on-mac-os-x*

"I have been banging my head against this error today on OSX Yosemite with MySQL 5.7 recently updated with Homebrew. Following suggestions on StackOverflow and elsewhere, I hunted around after my.cnf files all of which specified bind-address=0.0.0.0. I even removed and reinstalled MySQL following these instructions and then reinstalled using brew install mysql. Still no remote connections allowed.
categories: dev

It wasn't until I ran `ps -ax | grep mysql` and noticed that the bind address was being passed in the launch command (thus overriding any my.cnf files) that I dug some more and found out that Homebrew binds MySQL to 127.0.0.1 by default.

Editing `~/Library/LaunchAgents/homebrew.mxcl.mysql.plist` by changing `--bind-address=127.0.0.1` to `--bind-address=0.0.0.0` solved my problem (the latter should be changed to a specific IP address if this isn't just a development machine).

I feel this is a vital piece of information that was lacking from most of the resources I consulted so hopefully posting this here will help someone else!

**EDIT:** As LeandroCR indicated in the comments, running brew services restart mysql will overwrite the plist file in LaunchAgents with the default one, leading to MySQL mysteriously refusing connections again. So better advice than what I originally wrote is the following:

**Edit** `/usr/local/Cellar/mysql/<yourversion>/homebrew.mxcl.mysql.plist` and replace`--bind-address=127.0.0.1` with `bind-address=*` or `--bind-address=0.0.0.0` (see MySQL documentation on bind-address)
Restart mysql using brew services restart mysql
Then MySQL should continue to accept non-local connections from then on - until you reinstall it, presumably."