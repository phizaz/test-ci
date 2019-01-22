---
layout: post
title: Python subprocess calling a python script
date: 2016-08-03 08:37:13.000000000 +07:00
categories: dev
tags: python
---
If you are going to run another python script (using Python), don't forget to use `sys.executable` for Python interpreter's path

Something like this:

```
import subprocess
import sys

subprocess.call([sys.executable, 'script.py'])
```

This way you can guarantee that a script being called is run under the same Python's environment as the caller.
