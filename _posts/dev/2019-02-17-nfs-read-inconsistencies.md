---
layout: post
title: NFS file attribution caching causes reading inconsistency in multi-producer scenario
categories: dev
tags: nfs
---

With two producers e.g. local and remote producers, the changes made locally might need time to be acknowledged by the remote. 

Even without file caching this could still be a problem, I experience this first-hand while using Python. I think Python might use some kind of file attribute to determine file updates. NFS has this which is called "attribute caching".

If you mount NFS with the option `actimeo`, it enables this attribute caching mechanism. To disable it consider using `noac` option. 

There is a [mention](https://blogs.sap.com/2015/11/20/performance-impact-of-disabling-nfs-attribute-caching/) about performance degradation of `noac` option. The source suggests that `actimeo=0` has lower performance impact comparing to `noac` .

Personally, I see `noac` and `actimeo=0` to be too much a drag on performance. I now use `actimeo=3` (3 seconds) and see a much lower drag.

[Reference](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-ubuntu-16-04) NFS mounting configuration in fstab:

```
203.0.113.0:/home       /nfs/home      nfs auto,nofail,noatime,nolock,intr,tcp,actimeo=1800 0 0
```

[**NFS client documentation**](https://linux.die.net/man/5/nfs)