---
layout: post
title: SSH Tunneling - Connecting to a forbidden port
date: 2016-10-14 11:57:04.000000000 +07:00
categories: dev
tags: ssh
---
Refer to:
http://blog.trackets.com/2014/05/17/ssh-tunnel-local-and-remote-port-forwarding-explained-with-examples.html

You are blocked from making connection to a port, say, 6666. Luckily, you still can connect to a remote server via port 22 (ssh).

We can do SSH tunneling to get through the firewall via port 22 (which is not blocked here) and use a pivotal server to connect to the port 6666 relaying all the data back to us behind the firewall.

```
ssh -L <local_port>:<target_host>:<target_port> <pivotal_host>
```

In this case, it might be:

```
ssh -L 22:targethost.com:6666 me@pivotalhost.com
```

Then, you can ssh, via the pivotalhost.com, to the targethost.com using 

```
ssh me@localhost [-p 22]
```

Note : use `localhost` here because ssh has bound the port 22 on this machine.

Wow!
