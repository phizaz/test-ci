---
layout: post
title: Python turn sync functions to async and awaitable using threads
date: 2017-02-03 16:27:45.000000000 +07:00
tags: python
---
Not all io bound commands are ported to asynchronous reducing somehow the worthy of **asyncio** library. In fact, we can interpolate the use of both async and sync functions with relative ease of implementation.

I will try using threading interfaces to do the job, in the end I will `force_async` on a synchronous function and turn it into async one.

```
def force_async(fn):
    '''
    turns a sync function to async function using threads
    :param sync function:
    :return async funciton:
    '''
    from concurrent.futures import ThreadPoolExecutor
    import asyncio
    pool = ThreadPoolExecutor()

    def wrapper(*args, **kwargs):
        future = pool.submit(fn, *args, **kwargs)
        return asyncio.wrap_future(future)  # make it awaitable

    return wrapper
```

Usage:

```
@force_async
def long_blocking_function():
    import time
    time.sleep(10)
    return True

import asyncio
async def run():
    a = long_blocking_function()
    b = long_blocking_function()
    return await asyncio.gather(a, b) # [True, True] in 10 seconds
```

The main idea of the trick is to run a sync task in a thread (returning a future object, `coroutine.Future`) and then turn this future object into a `asyncio.Future` object. Obivously, this future object will be resolve when the sync function is done, and also is **awaitable**.

If you're looking for turning async function into sync function, which is much more obivous, see [here](https://blog.konpat.me/python-turn-async-function-back-to-sync/)
