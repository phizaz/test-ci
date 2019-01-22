---
layout: post
title: Matplotlib Set Figure Size
date: 2016-11-23 06:26:17.000000000 +07:00
categories: dev
tags: matplotlib
---
```
import matplotlib.pylot as plt
fig, axes = plt.subplots(...)
# ndarray size [w, h] in inches
# with default DPI set to 100
size = fig.get_size_inches() * 2 
fig.set_size_inches(size[0], size[1])
```

The code snippet above set the figure size to be **4 times** as large as the **default 8 x 6 sq.inches**, while keeping DPI to stay the same this will make things appear to be smaller.
