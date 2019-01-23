---
layout: post
title: 'Cygwin: To use Windows Python in shebang'
date: 2017-10-23 06:37:22.000000000 +07:00
categories: dev
tags: cygwin
---
Create a bash script as a proxy:

Filename `wpython` save it some where in your `$PATH`
```bash
#!/bin/bash
path=$1
# remove the first argument
shift
python.exe $(cygpath -w $path) $@
```

Now, we can use this shebang:

```bash
#! /usr/bin/env wpython
```

The code will convert Cygwin path back to Windows path, and thus compatible with Windows Python.
