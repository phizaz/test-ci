---
layout: post
title: Setup A Deep Learning Engine With Ubuntu 16.04 + Nvidia Pascal + Nvidia Docker
date: 2016-09-05 13:45:28.000000000 +07:00
categories: dev
tags: ubuntu cuda
---
I have seen a lot of instruction on building and setting up a machine learner, but with nvidia-docker I practically have seen so little relatively.

Using nvidia-docker is a good thing. It simplifies so much the complex part installing a large bunch of dependencies before you can start developing and running your deep learning algorithms.

With nvidia-docker you practically type `nvidia-docker run ...` and it just works like you would have intended to with a kind of magic with docker.

One question arises, however, if I were to use nvidia-docker what exactly would I have to install on my computer?

It's kind a obvious for me to look back, but not so for me at the time figuring it out.

Obviously, the first thing you can't miss out is the `docker` itself, simple because the nvidia-docker is built upon it.

So, if you don't have it in your machine already, here is a good place to start https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-16-04

Second thing, you need a working driver for your nvidia pascal graphic card, which means GTX1060, 70, 80 and Titan. You will need a nvidia graphic driver better than 367.44 (the first version of the base line graphic driver to support Pascals), or if you to test on more recent drivers you can go to 370 line which is less stable.


```
# if you don't have the 'add-apt-repository'
apt-get install software-properties-common
# begin !!
sudo add-apt-repository ppa:graphics-drivers/ppa
sudo apt update
# list all your choices
sudo apt search nvidia
# install driver 367.44 (at the time of writing)
sudo apt install nvidia-367
# install driver 370.23 (at the time of writing)
sudo apt install nvidia-370
```

It's important to mention CUDA driver and CUDA toolkit are packed inside the `nvidia/cuda` docker's image, so we not need to install it as prerequisites. That simple means you have no whatsoever to download the CUDA Toolkit, which is 1.5 GB large, from the nvidia website, it's already packed inside the image.

Third and final, you just need to install the `nvidia-docker`, for now you have all the prerequisites of it. Please visit https://github.com/NVIDIA/nvidia-docker for the latest installation.

Actually, if you read the prerequisites mentioned in nvidia-docker you'll see: 

```
GNU/Linux x86_64 with kernel version > 3.10
Docker >= 1.9 (official docker-engine only)
NVIDIA GPU with Architecture > Fermi (2.1)
NVIDIA drivers >= 340.29 with binary nvidia-modprobe
```

And whenever you want to run a docker, run `nvidia-docker` instead:

```
nvidia-docker run --rm nvidia/cuda nvidia-smi
```
