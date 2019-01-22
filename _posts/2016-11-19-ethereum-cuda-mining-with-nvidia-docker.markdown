---
layout: post
title: Ethereum CUDA Mining with nvidia-docker
date: 2016-11-19 08:34:52.000000000 +07:00
tags: cuda, crypto
---
Somebody has done quite a good job : https://github.com/Anthony-Tatowicz/docker-ethminer

But, if you're using Nvidia's Pascal cards, you're better off using CUDA 8.0 not 7.5 as of the above's Dockerfile says.

So the new Dockerfile should be begin with: 
```
FROM nvidia/cuda:8.0-devel-ubuntu14.04
```

But, if you rush to building it now, if you like me, you will find some errors during `cmake`, here is one of them : 

```
Building NVCC (Device) object libethash-cuda/CMakeFiles/ethash-cuda.dir/ethash-cuda_generated_ethash_cuda_miner_kernel.cu.o
```

Fortunately, there are others facing the very same problem as well, here is the thread: https://forum.ethereum.org/discussion/2227/cuda-miner/p34

And here is the response from the author of CUDA cpp-ethereum, namely *Genoil*: 

> **@Cubirez** yes ran into that myself too. This was caused by somebody who tried to make copatibility with Fedora work. I'll fix it in the rep later, but for now, add --std=c++11 to the NVCC flags in CMakelists.txt in libethash-cuda folder 

So, actually, you have to make some change to the *cloned* source. 

Here, I will change the course of the Dockerfile a bit, so that we can do the change : 

```
git clone https://github.com/Genoil/cpp-ethereum.git
cd cpp-ethereum/libethash-cuda
vim CMakeLists.txt
```

Change from 
```
set(CUDA_NVCC_FLAGS ${CUDA_NVCC_FLAGS};--disable-warnings;--ptxas-options=-v;-use_fast_math;-lineinfo)
```
To, by adding `--std=c++11;` : 
```
set(CUDA_NVCC_FLAGS ${CUDA_NVCC_FLAGS};--std=c++11;--disable-warnings;--ptxas-options=-v;-use_fast_math;-lineinfo)
```

Now, to the Dockerfile, instead of cloning we will be copying instead: 
```
COPY cpp-ethereum /cpp-ethereum
RUN cd cpp-ethereum \
    && mkdir build \
    && cd build \
    && cmake -DBUNDLE=cudaminer -DCOMPUTE=61 .. \
    && make -j8 \
    && mkdir /data
```
Note : I added `-DCOMPUTE=61` flag because I use GTX 1060, this should be optional, but it feels better being explicit.

**It should work now!**

Try : 
```
nvidia-docker run -it --rm <yourimage> /cpp-ethereum/build/ethminer/ethminer -U --benchmark
```
Note : use `-U` not `-G`; since `-G` = opencl, `-U` = cuda

I got something like

```
[CUDA]:Using grid size 8192, block size 128

Benchmarking on platform: CUDA
Preparing DAG for block #0
Warming up...
  ℹ  08:21:53|cudaminer0  set work; seed: #00000000, target:  #000000000000
  ℹ  08:21:53|cudaminer0  Initialising miner...
Using device: GeForce GTX 1060 6GB (Compute 6.1)
Generating DAG for GPU #0
CUDA#0: 0%
CUDA#0: 6%
CUDA#0: 12%
CUDA#0: 19%
CUDA#0: 25%
CUDA#0: 31%
CUDA#0: 38%
CUDA#0: 44%
CUDA#0: 50%
CUDA#0: 56%
CUDA#0: 62%
CUDA#0: 69%
CUDA#0: 75%
CUDA#0: 81%
CUDA#0: 88%
CUDA#0: 94%
Trial 1... 18874368
Trial 2... 19223893
Trial 3... 19223893
Trial 4... 19223893
Trial 5... 19223893
min/mean/max: 18874368/19153988/19223893 H/s
inner mean: 6407964 H/s
```
