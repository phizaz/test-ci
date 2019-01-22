---
layout: post
title: SSH using docker
date: 2017-02-04 09:39:12.000000000 +07:00
categories: dev
tags: docker
---
Let's say you want to `ssh` using docker, and also you want to be able to access all the settings you have with our old ssh, here is how you do it !

Dockerfile:

```
FROM debian:jessie

RUN apt-get update
RUN apt-get install -y --no-install-recommends openssh-client

VOLUME ["/ssh"]

COPY entrypoint.sh /usr/local/bin/
ENTRYPOINT ["entrypoint.sh"]
```

entrypoint.sh:

```
#!/usr/bin/env bash

# install ssh keys (/ssh -> ~/.ssh)
cp -R /ssh /root/.ssh
chmod -R 500 /root/.ssh

# entrypoint
# redirects all arguments to the ssh command
# 'exec' will replace the process (might be useful for redirecting SIGTERM)
exec ssh $@ 
```

If we mount our `.ssh` directory to the `/ssh` of the container, it will automatically copy (with the right permission) its contents to its `/root/.ssh`. Thus, we will be able to use all ssh as if it were the one on our host machine.

Build the Dockerfile:

```
docker build -t ssh .
```


Set alias for your `cmd.exe`: 

```
alias ssh-docker=docker run --rm -it -v %HOME%\.ssh:/ssh:ro ssh $*
```

Note: `$*` at the end of alias is to accept arguments.

Enjoy:

```
ssh-docker test@host
```
