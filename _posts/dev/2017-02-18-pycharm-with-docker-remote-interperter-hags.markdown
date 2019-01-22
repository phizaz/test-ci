---
layout: post
title: Pycharm with Docker Remote Interpreter Hangs
date: 2017-02-18 07:57:32.000000000 +07:00
categories: dev
tags: docker python
---
**Update: It has been solved in **Python 2017.1 EAP****
categories: dev

I reported the issue at https://youtrack.jetbrains.com/issue/PY-22786

> I use Docker for Windows on Windows 10 with Hyper-V
Docker version 1.13.1, build 092cba3
**Docker image: phizaz/sklearn (basically installs numpy, scipy, scikit-learn)**
Using this image, PyCharm will slowly update the skeleton and **even the bar is full it won't finish**, I even left the computer for a day and it didn't finish.
categories: dev

Here is the captured screenshot: 

![](/content/images/2017/02/2017-02-17-23_28_06-adaboost-test----C__Users_Ta_Projects_guru-kasikorn_adaboost-test----..._test.py.png)

I think it might relate to this issue as well: 

https://youtrack.jetbrains.com/issue/PY-21505

![](/content/images/2017/02/2017-02-17-19_01_18-docker-compose-remote-interpreter-skeleton-generation-hangs-_-PY-21505.png)

Because not only it won't finish, sometimes I encountered Pycharm not responding, as even so Docker not responding as well. (after closing Pycharm docker became responsive as usual)

**For now, I'll use 2017.1 EAP instead.**
