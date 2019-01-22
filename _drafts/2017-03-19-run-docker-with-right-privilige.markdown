---
layout: post
title: Run docker with right privilige
---
Introducing **gosu**, it's not mine I just stumbled upon it, please visit https://github.com/tianon/gosu for more information.

In short, it's the **preferred way** to use `su` or `sudo`

The problem it solves is when want to run a specific command with a specific user privileges in a docker container while keeping running the container with `root` privileges.

It replaces the way you call your command this way:

```
# from
command
# to
gosu 1000:1000 command # where 1000 is the desired user
```

To use it within the container you have to install it, of course, read https://github.com/tianon/gosu/blob/master/INSTALL.md, for the purpose of copy and paste, I'll put it here:

```
FROM debian:jessie

ENV GOSU_VERSION 1.10
RUN set -x \
    && apt-get update && apt-get install -y --no-install-recommends ca-certificates wget && rm -rf /var/lib/apt/lists/* \
    && dpkgArch="$(dpkg --print-architecture | awk -F- '{ print $NF }')" \
    && wget -O /usr/local/bin/gosu "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-$dpkgArch" \
    && wget -O /usr/local/bin/gosu.asc "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-$dpkgArch.asc" \
    && export GNUPGHOME="$(mktemp -d)" \
    && gpg --keyserver ha.pool.sks-keyservers.net --recv-keys B42F6819007F00F88E364FD4036A9C25BF357DD4 \
    && gpg --batch --verify /usr/local/bin/gosu.asc /usr/local/bin/gosu \
    && rm -r "$GNUPGHOME" /usr/local/bin/gosu.asc \
    && chmod +x /usr/local/bin/gosu \
    && gosu nobody true \
    && apt-get purge -y --auto-remove ca-certificates wget
```

Consider this to be the new based image.

Variable substitution

Your configuration options can contain environment variables. Compose uses the variable values from the shell environment in which docker-compose is run. For example, suppose the shell contains EXTERNAL_PORT=8000 and you supply this configuration:

web:
  build: .
  ports:
    - "${EXTERNAL_PORT}:5000"

When you run docker-compose up with this configuration, Compose looks for the EXTERNAL_PORT environment variable in the shell and substitutes its value in. In this example, Compose resolves the port mapping to "8000:5000" before creating the web container.

If an environment variable is not set, Compose substitutes with an empty string. In the example above, if EXTERNAL_PORT is not set, the value for the port mapping is :5000 (which is of course an invalid port mapping, and will result in an error when attempting to create the container).

You can set default values for environment variables using **a .env file, which Compose will automatically look for.** Values set in the shell environment will override those set in the .env file.

```
$ unset EXTERNAL_PORT
$ echo "EXTERNAL_PORT=6000" > .env
$ docker-compose up          # EXTERNAL_PORT will be 6000
$ export EXTERNAL_PORT=7000
$ docker-compose up          # EXTERNAL_PORT will be 7000
```


