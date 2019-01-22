---
layout: post
title: Docker compose network ip specified
date: 2017-01-28 17:59:15.000000000 +07:00
tags: docker
---
Normally, the IP address is not really important in the connection between Docker containers, becasue we can just use the hostnames, which are easier to use and more intuitive to use. 

However, there are times when we really need to use specific IP addresses, and the Docker does provide us the mean to do it.

Here is an unrealistic example of **docker-compose.yml**, in which we will define A, B, C and the network called "my-network". With this network, we will link A and B together. Even so, we still have the "default" network which is, by the way, created automatically, and we use this network to connect between A and C in the normal fashion way through hostnames.

```
version: '2'

# define the network
networks:
    my-network: 
        ipam:
            config:
                - subnet: 172.16.1.0/24
                  gateway: 172.16.1.1 # you cannot allocate this address to any of the containers

services:
    A:  
        networks:
            default:
            my-network:
                ipv4_address: 172.16.1.2 # you can also let the DHCP do the job

    B:
        networks:
            my-network:
                ipv4_address: 172.16.1.3
    
    C:
        networks: # with default, you don't have to write this
            default:
```

Now, if you were to `exec` into the B you will find yourself able to ping "172.16.1.2" via the **my-network**. On the other hand, if you were to `exec` into the C you will be able to ping the A using the hostname.
