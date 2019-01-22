---
layout: post
title: OpenCV 3 macOS 10.12 Sierra QTKit not found build issue
date: 2016-10-12 16:23:28.000000000 +07:00
categories: dev
tags: mac
---
According to : https://github.com/Homebrew/homebrew-science/issues/4104

Please try : 
```
brew reinstall opencv3 --HEAD --with-python3 --with-ffmpeg --with-tbb --with-contrib
```
