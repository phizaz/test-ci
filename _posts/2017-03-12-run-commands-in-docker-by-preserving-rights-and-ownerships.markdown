---
layout: post
title: Run Commands in Docker by Preserving Rights and Ownerships
date: 2017-03-12 07:30:02.000000000 +07:00
tags: docker
---
```
docker run --user ${UID} ...
```

To run commands in a container by using the same user as `whoami`. 

It's not perfect though, it has the very limitation that the container won't know the actual user name of the user `${UID}` which, in many cases, is not acceptable.

If you want to make it more realistic, make the container really aware of this user and its username, I'm afraid that you might have to mount the `/etc/passwd` to the container which is in most ways not favorable.

```
docker run --user ${UID} -v /etc/passwd:/etc/passwd:ro ...
```

But, it managed to work quite well. 

Another way that might serve you well is to run the container with `root` just like always, but run specific commands with hand-curated specific users like:

In `Dockerfile`: 

```
# run with user id
RUN sudo -u "#<userid>" <command>
# run with username
RUN sudo -u "<username>" <command>
```

Note: you have to `apt-get install sudo` to do this. You still need to mount the `/etc/passwd`. 
