---
layout: post
title: 'Python SafeQuitProcess: On termination the process also propagates the termination
  signal to all its children'
date: 2016-08-07 08:47:46.000000000 +07:00
tags: python
---
If a process has children, kill the parent won't be enough to guarantee "on orphaned process". You basically have to recursively kill all the children process as well.

Here is the way to do it.

<script src="https://gist.github.com/phizaz/4ee94fd75ae00fca5bc69232f4aa5b85.js"></script>
