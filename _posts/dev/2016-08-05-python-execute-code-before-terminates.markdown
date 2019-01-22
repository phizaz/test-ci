---
layout: post
title: Python Execute Code Before Terminates
date: 2016-08-05 04:22:06.000000000 +07:00
categories: dev
tags: python
---
```
def cleanup():
    ... clean up script ...

# on exit
atexit.register(cleanup)
```
