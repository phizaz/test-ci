---
layout: post
title: Access to a Docker for OSX Host from a container
date: 2016-09-13 16:55:39.000000000 +07:00
categories: dev
tags: docker mac
---
Setting `network` as `host` is not quite enough for this case.
The working solution is here (according to https://forums.docker.com/t/access-host-not-vm-from-inside-container/11747/10):

```
sudo ifconfig lo0 alias 172.16.123.1
docker run -it --rm busybox ping 172.16.123.1
```

The trick is to give the host's lo0 interface a new IP which can be accessed from the container.
