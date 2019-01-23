---
layout: post
title: Python turn async function back to sync
date: 2017-02-04 06:56:24.000000000 +07:00
categories: dev
tags: python
---
Actually, this is straightforward and those who works with `asyncio` library use all the time. 

It is `asyncio.get_event_look().run_until_complete(...)`

Now, I will just wrap it into a nicer package, like this:

```python
def force_sync(fn):
    '''
    turn an async function to sync function
    '''
    import asyncio

    @functools.wraps(fn)
    def wrapper(*args, **kwargs):
        res = fn(*args, **kwargs)
        if asyncio.iscoroutine(res):
            return asyncio.get_event_loop().run_until_complete(res)
        return res

    return wrapper
```

Here is how to use it:

```python
@force_sync
async def run():
    ... do some async works ...

results = run()
```

For those who look for the vice versa, turning a sync to async, I have wrote it [here](https://blog.konpat.me/python-turn-sync-functions-to-async/)

