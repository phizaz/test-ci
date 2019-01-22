---
layout: post
title: Python subprocess with stdout as a string
date: 2016-08-03 08:40:43.000000000 +07:00
categories: dev
tags: python
---
You can use `subprocess.check_output` which will redirect `stdout` to the function call's return value

Something like:
```
import subprocess

output = subprocess.check_output(['which', 'python'])
print(output)
```
