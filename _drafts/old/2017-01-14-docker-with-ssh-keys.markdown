---
layout: post
title: Docker with SSH keys
---
Wondering if I can use pure docker without any installed programs in my system ... 

I would like to start with how to ssh to the known system using my keyfiles. 

Well, the first approach is to mount the `.ssh` folder in my home (I'm using Windows 10 now).

Let's start with `docker-compose.yaml` file like this:

```
version: '2'

services:
    bash:
        ...
        volumes:
            - ~/.ssh:/root/.ssh:ro
```

Mount `{home}/.ssh` to the `/root/.ssh` so that in the container we can just `ssh` and then everything should work right ? Well, not the case for Windows because the mount drives have not strong enough premission for ssh to run.

In this case, we have to look for something more managable. This time I will try mounting to some temporary place and then copy the content to the `/root/.ssh` and `chmod 600 -R `/root/.ssh`. Doing so, it should work.

We create a `run.sh` file

```
mkdir -p /root/.ssh
cp /root/tmpssh/* /root/.ssh/
chmod -R 600 /root/.ssh
bash
```

Also create a Dockerfile:

```
FROM <somewhere> 
COPY run.sh /run.sh
```

And also alter a docker-compose this way:

```
version: '2'

services:
    bash:
        ...
        volumes:
            - ~/.ssh:/root/tmpssh:ro
        command: sh /run.sh
```

Start the process like this:

```
docker-compose run bash
```

Now, the bash has an embedded `.ssh` folder and you can start ssh-ing now.
