---
layout: post
title: Conda (and Anaconda) for python package management and virtual envs
date: 2017-05-21 14:49:51.000000000 +07:00
categories: dev
tags: python
---
### Of installing `scipy`

*I have once wrote a way to install `scipy` on Windows 64 bit [here](https://blog.konpat.me/python-3-on-windows-2/)*

`scipy` is notoriously hard to install on Windows. It's not a bug or anythnig, it is by design.

Python is a super dynamic language designed to be compatible with C extensions.

That means, Python is affordable to be a little bit slow while keeping its interface with C extensions easy and fast. The advent of GIL (Global Interpreter Lock) in CPython is the testimony of this.

(Somebody has summarized why Python (CPython) uses GIL even though it renders multi-core CPUs useless, it's a good and short read [here](https://softwareengineering.stackexchange.com/questions/186889/why-was-python-written-with-the-gil/186909#186909))

In this case, Python can be as dynamic as it wants, and let the C extensions do the dirty and fast part.

However, it comes with a cost, C is not a **write once run anywhere** programming language. Most of the time, you need to compile it on your machine to guarantee the usability. If you have a good C compiler installed this should not be much of the problem. But most of us, especially Windows users, don't.

Many people have been trying to circumvent this limitation, thus far I have concerned, many packages can now easily be installed by `pip` without much to do with any compiler i.e. numpy, that means somebody has compiled it beforehand for you. 

But that doesn't include `scipy`. You cannot install it (as of early 2017), you cannot install it using `pip` without proper configured compiler on Windows with Python 64 bit version installed.

There is some other way namely **conda** (bare `conda` can be installed via Miniconda) and **Anaconda** (which is a full-fledged python install for scientific use, also comes with `conda` you have no need to install both) by one of which you can install numpy without a fuss. 

That's why **Conda** (and **Anaconda**) is an intriguing choice for this.

### What's it (conda) actually ?

*Somebody has wrote about this, I have no need to rewrite it anymore, it is a good read I promise [here](https://jakevdp.github.io/blog/2016/08/25/conda-myths-and-misconceptions/)*

In short, **conda** is a package manager. 

Hey, **pip** is also a package manager isn't it ? Why reinventing the wheel ? 

**pip** is not a *general* package manager, it is a *Python* package manager.

**conda** is far more ambitious than that. It aims to be a package manager for *anything* on *any operating system*. 

Of course, **conda** is also good at Pythonic things partly because it is written in Python. That means its installation also comes with some version of Python, and it will ask you, upon installation, whether you would like to use its bundled version of Python as your system default.

If **conda** is a general package manage which originates from Python and is good at it, with os-agnostic in mind, **conda** should be able to install `scipy`. And yes, it is.

### Of virtual environments

It's undeniable that for developers standalone Python would not do much good. Because they cannot bend them selves to do only **one project at a time**. That's where virtual environment comes so that each project will be able to have its own, hopefully, isolated environment to run. 

To achieve that, there is a pip package called **virtualenv** for that. On other side, **conda** has been designed to support multiple environments from the start.

If **virtualenv** is enough to do your job, I doubt you have any good reason to use **conda** anymore. But, if still you seed to work with C extensions, you don't have many choices left.

### Of terms: conda, miniconda, anaconda

I like to think that **Anaconda** is a full-fledged bundle for scientist doing jobs in Python. That means Anaconda is super easy to install one package with more than a hundred pre-compiled packages ready to install.

Bundled with Anaconda is **Conda**, a command line program for package management and virtual environment management. 

So, if you want to create a new environment you can use `conda create --name=my_new_env python=3.6` to create one. 

Or if you want to install a new package (any package not limited to Python), you can `conda install scipy`, it will install on the *current* environment most likely to be the *root* environment. 

You can specify the environment to be installed using `conda install --name=my_new_env scipy`. 

Or if you are tired of retyping the environment name time and time again you can explicitly change current environment using `source activate my_new_env` and then `conda install my_new_env` will be installing to *my_new_env* instead of *root*

Now, if **Conda** is what you want, but you don't want to install the heavy handred-package-bundle **Anaconda** you still can install only **conda** via **Miniconda**.

So, in short, **Miniconda** is a lean **Anaconda** which contains almost only **Conda**.

### Of conda channels

When you installing a package, **conda** actually searches its **channel** for the matched package to retrieve and install. The default channel is called *defaults*, but it is not the *only* channel out there.

*defaults* channel contains only so many packages and you will occasionally be trying to install packages that *defaults* channel cannot offer.

But don't lose hope yet! You can search for it, there are many other channels as I said. Let's say you want to install *fastcluster*, which is a python package and is not offered by *defaults* channel, you can try searching google for it "conda fastcluster" is a good start. 

![](/content/images/2017/05/2017-05-21-21_36_52-conda-fastcluster---Google-Search.png)

There are many candidate channels, but I tend to be fond of a *larger* channel. In this case, **conda-forge** is the winner. 
categories: dev

(If you want to know more about **conda-forge** you can see its homepage [here](https://conda-forge.github.io/))

So, to install the *fastcluster* you can either explicitly specify on installing like `conda install --channel=conda-forge fastcluster` or add the channel to your list of channels so that it will search in that first `conda config --add channels conda-forge`.

(**Conda** has its way to resolve package conflicts between channels [here](https://conda.io/docs/channels.html))

There are still packages that no channel provides. In this case, you can fallback to the good old **pip** and hope that you will be able to install it.

It is worth noting that **conda**'s lack of full pip package repertoire must not be the deal breaker since you can always fall-back to using pip, and it, in no way, decreases the pip ability to successfully install any package on your system.

