---
layout: post
title: Dealing with Big Vars in Python
date: 2016-11-13 18:51:45.000000000 +07:00
categories: dev
tags: python
---
I have encountered memory leak in python, and It's not fun at all. Its obscuration consumed so much of energy and time. 

I have come to a policy of mine. When dealing with big vars about which we really want to minimize the memory footprint, deallocate the memory as soon as it's not being used any more in a context.

```
A = big var
... use A ...
A = None
import gc
gc.collect()
```

Mainly, do the above ! But does it really make sense ? It sure does! Considering this case:

```
def get_a_big_val():
    return something big

A = get_a_big_val()
... use A ...
A = get_a_big_val()
... use A ...
```

How much memory do we need in this case ? Actually, it's more like 2 * A, since only one A is big enough we should avoid this.

```
import gc
A = get_a_big_val()
... use A ...
A = None
gc.collect()
A = get_a_big_val()
... use A ...
A = None
gc.collect()
```

Better memory foot print now.

Actually, from the case I have encountered which involves memory leak, doing this will solve the problem, if not it still forestalls the chance of memory leak by a good margin.
