---
layout: post
title: Nosetest  multi-process will run "TestCase" concurrently, but not smaller than
  that
date: 2016-08-04 03:41:43.000000000 +07:00
---
Using `--processes=4` which enables the multi-process testing mode for Noetest you might expect tests be run in fully concurrent, but here only each `TestCase` will be run that way, not a smaller unit than that.

```
import unittest
import os

'''
nosetest will run each class concurrently but each method sequentially
'''

class A(unittest.TestCase):

    def test_aa(self):
        import time
        time.sleep(1)
        self.assertEqual(0, os.getpid())

    def test_ab(self):
        import time
        time.sleep(1)
        self.assertEqual(0, os.getpid())

class B(unittest.TestCase):

    def test_ba(self):
        import time
        time.sleep(1)
        self.assertEqual(0, os.getpid())

    def test_bb(self):
        import time
        time.sleep(1)
        self.assertEqual(0, os.getpid())

class C(unittest.TestCase):

    def test_ca(self):
        import time
        time.sleep(1)
        self.assertEqual(0, os.getpid())

    def test_cb(self):
        import time
        time.sleep(1)
        self.assertEqual(0, os.getpid())
```
