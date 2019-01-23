---
layout: post
title: Python 3 (64 bit) + scipy on Windows
date: 2017-05-05 04:28:37.000000000 +07:00
categories: dev
tags: python
---
## I tried original one, but didn't support `scipy`

(x64) I just can't install `scipy` as a matter of fact, `scipy` doesn't provide pre-compiled solution for Windows in the first place, and installing python in way, I think, I have no hope for it.

So next, I go for another option which I can properly setup my complier, namely **cygwin**.

I have read somewhere, it will work if I install python (32 bit) instead. But why should I, this is 2017!?

## I tried cygwin ... it failed to build `scipy`

I first installed these packages from the cygwin's installer.

1. `python3`
2. `python3-devel` (you need this if you want to install packgaes like `numpy`)
3. `python3-pip`

After installation, Python 3 will take the name of `python3` to make it work as a default `python` I did some `ln -s` and some `export PATH` to make it present in the default `PATH`.

I tried to install `numpy` which I did install successfully. (arduously long install due to the compilation time)

Then, install `scipy` now it failed with the following error:

```
numpy.distutils.system_info.NotFoundError: no lapack/blas resources found
```

I didn't seem to find any solutions or workaround online ... so I paused there !

## So, I give `conda` a try ... It seems to work

You don't have to install the full-big **anaconda** to use `conda`, you can just install the smaller **miniconda** it will do the job.

I think `conda` has its own way to install packages, I think it still leverage the use of `pip` in some point but also with some modifications. *So in short*, `conda` is a front-end to multi-platform mulit-language package installation. 

When I install `numpy` `scipy` it seemed to me that `conda` also install `mkl` (I don't know much about this, it's like a math optimization library for Intel CPUs or something). 

But at the end, it did install `scipy` and `numpy` successfully ! yay ...

## Python's `multiprocessing` is slow under Windows

After happily using it for a while ... I spot that using `multiprocessing.Pool` is quite slow, it's slower than single core processing! I'm quite sure that I'm not new to this. I know how to make it work properly, at least under unix-like environment. 

So, I dug deeper into this problem ... which turned out to be problem with the Windows itself.

### It's not to blame `conda` for it, it's the problem between Windows and Python

As you know, `multiproccesing.Pool` using multiple processes to leverage a speed boost, which threading cannot in Python (because of the existance of Global Interpreter Lock).

This problem doesn't seem to have much problem in the user-end point of view, the usage of `multiprocessing` is itself easy enough, as long as you keep your program functional.

However, it is said that spawnwing a process under Windows environment is much much slower than in Linux. As been asked in here: http://stackoverflow.com/questions/8775475/python-using-multiprocess-is-slower-than-not-using-it. To me, the argument is quite valid. I have long heard that Windows favors multi-threading and Linux favors multi-processing, so it is sure that on which OS which one will be optimized.

Unfortunately, multi-threading for speed boost under computational intensive is not supported in Python, and multi-processing is not very well supported by Windows. I have left with almost zero option, it seems quite a deal breaker.

### `chunksize` might help

As a matter of fact, Windows' process is quite slow to start, this greatly reduces the viability of python's `multiprocessing.Pool`. If spawning a new process is slow, then should we spawn less processes and distribute a larger bit of work for each process instead ? This is exactly what `chunksize` parameter in `pool.imap` does.

```python
from multiprocessing import Pool

with Pool() as pool:
    for result in pool.imap(fn, jobs, chunksize=16):
       ....
```

So, in short, you should set `chunksize` somewhat larger than 1 and see if the problem mitigates.

I will try to use it for the moment ... hope I can live with it :D
