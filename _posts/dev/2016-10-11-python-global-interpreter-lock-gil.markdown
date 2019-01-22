---
layout: post
title: Python Global Interpreter Lock (GIL)
date: 2016-10-11 09:55:55.000000000 +07:00
categories: dev
tags: python
---
This is the cause of Python thread performance cap on only one cpu core.

- Python's thread is a real system thread
- GIL causing multi-core CPU to be slower (on cpu bound task)
- new GIL is coming ? better CPU bound task, worse I/O bound task (need some improvement)

**Just read :** 
http://www.dabeaz.com/python/UnderstandingGIL.pdf
