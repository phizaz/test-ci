---
layout: post
title: 'Python CatchableProcess: Catch exceptions in spawned processes'
date: 2016-08-07 08:39:37.000000000 +07:00
categories: dev
tags: python
---
Normally, a parent process and a spawned process will have no link in between, meaning thrown errors from each process will be quite independent. The parent is ignorant to the errors in the child process, and vice versa. 

I wrote this class to solve that problem, on calling `p.join()` on the parent process (`p` is the spawned process) all the thrown errors will be propagated to the parent as well.

<script src="https://gist.github.com/phizaz/dc45756db1197cee83bbdfa964a1b46a.js"></script>

