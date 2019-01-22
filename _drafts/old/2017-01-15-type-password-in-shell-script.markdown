---
layout: post
title: Type password in shell script
---
thread: https://download.pureftpd.org/pure-ftpd/doc/README.Virtual-Users

This can be useful especially when dealing with Dockerfiles because we have no access to the TTY during build. 

The way is quite straightforward which is just to `echo` through a pipe that connects to the receiver, like:

```
echo "password" | program_that_receieve_password 
```

Actually, it is possible to do this even the program asks for a repeat:

```
(echo "password"; echo "password") | program_that_receieve_password
```
