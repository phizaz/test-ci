---
layout: post
title: Python Multiprocessing's ForkingPickler error with __new__ missing positional
  arguments
---
```
Traceback (most recent call last):
  File "C:\tools\Anaconda3\envs\nofold-ssl\lib\multiprocessing\process.py", line 249, in _bootstrap
    self.run()
  File "C:\tools\Anaconda3\envs\nofold-ssl\lib\multiprocessing\process.py", line 93, in run
    self._target(*self._args, **self._kwargs)
  File "C:\tools\Anaconda3\envs\nofold-ssl\lib\multiprocessing\pool.py", line 108, in worker
    task = get()
  File "C:\tools\Anaconda3\envs\nofold-ssl\lib\multiprocessing\queues.py", line 345, in get
    return _ForkingPickler.loads(res)
TypeError: __new__() missing 2 required positional arguments: 'bitscores' and 'header'
```

I have a NamedTuple with no default values:

```
class Collection(NamedTuple):
    names: Optional[List[str]]
    sequences: Optional[List[str]]
    bitscores: Optional[np.ndarray]
    header: Optional[List[str]]
```

No! it's not the problem it is actually the `__iter__` overriding that made all these problems !

After assigning it some default values everything seemed to work just fine:

```
class Collection(NamedTuple):
    names: Optional[List[str]] = None
    sequences: Optional[List[str]] = None
    bitscores: Optional[np.ndarray] = None
    header: Optional[List[str]] = None
```

I don't yet understand why defaults are required in the case of multiprocessing.

It happens so that this is a consequence of other codebase, It's still kinda weird that it came up with this error.
