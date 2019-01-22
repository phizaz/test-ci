---
layout: post
title: Ubuntu Can't Verify SSL Certs
date: 2016-11-12 13:28:20.000000000 +07:00
tags: ubuntu
---
I found something like this while `wget` a file from `https` protocol: 
> Unable to locally verify the issuer's authority.

What it's saying is that its knowledge about ssl certs doesn't cover the one we're using.

Fortunately, it can be solved using :

```
apt-get install ca-certificates
```

And [here](https://launchpad.net/ubuntu/+source/ca-certificates) is the description of the package.

> PEM files of CA certificates to allow SSL-based applications to check for the authenticity of SSL connections.
