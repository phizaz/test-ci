---
layout: post
title: Docker MySQL to have default UTF8 encoding
---
[thread](https://github.com/docker-library/mysql/pull/14)

![](/content/images/2017/02/2017-02-12-01_48_43-Allow-setting-CHARACTER-SET-for-the-database-by-pstiasny---Pull-Request--14---do.png)

```
command:
    - --character-set-server=utf8
    - --collation-server=utf8_unicode_ci
```
