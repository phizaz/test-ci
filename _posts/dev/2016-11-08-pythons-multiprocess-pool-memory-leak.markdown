---
layout: post
title: Python's Multiprocess Pool Memory Leak
date: 2016-11-08 05:09:48.000000000 +07:00
categories: dev
tags: python
---
Update : I investigated further to the problem, and found a better solution. I will write a topic on that.
categories: dev

Actually, I don't know how this happen, just can't narrow it down enough to a pin point.

The situation can be described here:

```python
# no globals

def ...

def ...

def ...

def trigger():
	...
	pool = Pool()
	for each in pool.imap_unordered(memory_consuming_fn, some_list):
		...
	pool.close()
	pool.join()
	...

# run the trigger() function many times
```

Note : In my case the `memory_cosuming_fn` involves opening many large files, this could be the case (but I personally don't think so because the running context is separated from the main process, and is essentially freed up every time without fail).

Note2: For those who aren't familiar with `multiprocess`, it's essentially important to make sure that the global variables are not huge because it will be forked and duplicated ! esp. under Python 2. Moreover, it's much better if you keep the globals immutable.

The point is to run `trigger()` many times, may be in this fashion:

```python
for i in range(10):
	trigger()
```

After each run, not all memory are freed, more or less, so, I assume that memory leakage is encountered.

I strayed trying to make small changes to the code involving putting many `gc.collect()` to multiple places, still no luck.

Here are some of my unfruitful experiments:

1. Making sure that a new process is spawn for each job, `pool = Pool(maxtasksperchild=1)`.
2. Put `gc.collect()` to the beginning of the `memory_consuming_fn`.
3. Try deallocate large vars by `<var> = None` or `del <var>` and then `gc.collect()`.

And, here are some working experiments:

A. Declaring an initializer:
```python
def pool_init():
	import gc
	gc.collect()

pool = Pool(initializer=pool_init)
```

B. Put `gc.collect()` before declaring pool:
```python
import gc
gc.collect()
pool = Pool()
```

Note: both are not the same thing ! 
> If initializer is not None then each worker process will call initializer(*initargs) when it starts.

That means the A. runs `gc.collect()` in the context of pool processes not the main process as the B. would.

And this makes the A. to be kinda same of the 2. and the 1. ? Don't know why both of those don't work.

I don't know specifically why all the memory leak is gone, in part, because I don't really know where the leakages are. But, it's likely that the memory leakage is somewhere (very tight) before spawning the pool processes. **This might be some of the globals (if there is any) or the parameters of the `memory_consuming_fn` itself.**

