---
layout: post
title: Fast Pythonic Way to Calculate Average Distance Between Points
date: 2016-11-02 16:46:51.000000000 +07:00
tags: python
---
Finding an average distance between two sets of points (vectors) in a n-demensional euclidean space can be **slow** ... harnessing `O(a * b * n)` of time complexity, where `a` and `b` denote the numbers of points in each set.

A normal way to do this is something like:

```
import numpy as np
def dist(a, b):
    return np.linalg.norm(a - b)

def dist_cluster_avg(points_A, points_B):
    from itertools import product
    s = sum(dist(a, b) for a, b in product(points_A, points_B))
    return s / len(points_A) / len(points_B)
```

Even though, the `def dist(a, b)` is arguable a very fast distance function, looping over each pair of points can be inexorably slow, to some extent because of the Python itself.

There are so many ways out there to improve by the means of optimizations, it is important to note that the time complexity stays the same, but it is much better implemented.

I found this, using `cdist` from `scipy`, and `sum` from `numpy` :

```
def dist_cluster_avg_fast(points_A, points_B):
    from scipy.spatial.distance import cdist
    arr = cdist(points_A, points_B, metric='euclidean')
    return np.sum(arr) / (len(points_A) * len(points_B))
```

How much the gain ? You might ask, here it is.

```
points_A = np.random.rand(500, 100)
points_B = np.random.rand(500, 100)

dist_cluster_avg(points_A, points_B) => 2.569478988647461 seconds
dist_cluster_avg_fast(points_A, points_B) => 0.15470504760742188 seconds
```

Much faster huh? 
