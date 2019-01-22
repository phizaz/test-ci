---
layout: post
title: Use Nodemon with Docker mount drives
date: 2017-01-19 09:25:15.000000000 +07:00
categories: dev
---
```
nodemon -L ...
```

Tell `nodemon` to use **legacy** polling for file updates, and yes of course, it is slower than the traditional file updates checking mechanisms (like inotify). But hey, it works and it might be the only way that works.
categories: dev
