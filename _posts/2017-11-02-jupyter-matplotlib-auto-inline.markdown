---
layout: post
title: Jupyter matplotlib auto inline
date: 2017-11-02 12:17:25.000000000 +07:00
tags: matplotlib
---
You might need to use this magic:

```
%matplotlib inline
```

Every time you want to force the inline output for a graph figure generated from matplotlib, which is actually most of the times.

There is a way to make this "automatic" by configuring the file `.jupyter/jupyter_notebook_config.py`:

By adding:

```
c.InteractiveShellApp.matplotlib = "inline"
```

And just reload the notebook in your browser, this should work automatically without the explicit magic anymore.
