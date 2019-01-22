---
layout: post
title: Docker-compose to link outside containers ?
date: 2017-01-15 09:36:11.000000000 +07:00
categories: dev
tags: docker
---
Let say we have the two docker-compose files in separate directories which we want to link together.

First, we have:

```
version: '2'
services:
    A:
        image: nginx
```

It is important to know the specific **network name** (external_name) to be used by the first `docker-compose up`. We'll need to run the first with `-p` to define the project name which is a part of the network name in `{project_name}_default` fashion.

In this case, run `docker-compose -p A up` it will automatically create **A_default** as a default network for the compose.

Second, we have:

```
version: '2'
networks:
    A_network:
        external:
            name: A_default
services:
    B:
        image: debian
        command: bash
        networks:
            - default
            - A_network
```

Now, run it, this time I will just run `docker-compose run B` for the purspose of accessing the shell. 

And try, pinging A (across the network).

```
root@a4d147584211:/# ping A
PING api (172.21.0.2): 56 data bytes
64 bytes from 172.21.0.2: icmp_seq=0 ttl=64 time=0.101 ms
64 bytes from 172.21.0.2: icmp_seq=1 ttl=64 time=0.072 ms
64 bytes from 172.21.0.2: icmp_seq=2 ttl=64 time=0.072 ms
64 bytes from 172.21.0.2: icmp_seq=3 ttl=64 time=0.073 ms
```

Understand that, **B** also has its own default network, but it also taps to the external one as defined in **A_network** which ultimately connects to the **A_default** external network created by the first compose.

