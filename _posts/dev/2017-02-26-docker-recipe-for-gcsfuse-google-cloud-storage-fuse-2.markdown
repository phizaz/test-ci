---
layout: post
title: Docker Recipe for "gcsfuse" (Google Cloud Storage FUSE)
date: 2017-02-26 18:08:49.000000000 +07:00
tags: docker cloud
---
### What's gcsfuse ? 

from: https://cloud.google.com/storage/docs/gcs-fuse

> Cloud Storage FUSE is an open source FUSE adapter that allows you to mount Google Cloud Storage buckets as file systems on Linux or OS X systems. 

`gcsfuse` main repository: https://github.com/GoogleCloudPlatform/gcsfuse

### Dockerize it

This is the product of many hours of my trail-and-error.

In the `Dockerfile`: 

```
FROM debian:latest

RUN apt-get update
RUN apt-get install -y curl lsb-release

RUN export GCSFUSE_REPO=gcsfuse-`lsb_release -c -s` \
    && echo "deb http://packages.cloud.google.com/apt $GCSFUSE_REPO main" | tee /etc/apt/sources.list.d/gcsfuse.list \
    && curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -

RUN apt-get update
RUN apt-get install -y gcsfuse

# need to run command with given user_id
RUN apt-get install -y sudo

# set running environments
ENV GOOGLE_APPLICATION_CREDENTIALS /credential.json
ENV BUCKET_NAME undefined-bucket-name 
ENV UID 1
ENV DATA_DIR /mnt

COPY entrypoint.sh /
ENTRYPOINT ["sh", "/entrypoint.sh"]
```

In the `entrypoint.sh`: 

```
# just in case ...
chown -R ${UID}:${UID} ${DATA_DIR} 

# run with given user
exec sudo -u "#${UID}" gcsfuse --foreground --key-file=${GOOGLE_APPLICATION_CREDENTIALS} ${BUCKET_NAME} ${DATA_DIR} 
```

You may build it using: `docker build -t gcsfuse .`

The run command:

```
BUCKET_NAME=<your_bucket_name> \
UID=${UID} \
docker run -it --rm --privileged \
  -v /etc/passwd:/etc/passwd:ro \
  -v <path_to_credentials>:/credential.json:ro \
  -v <your_mount_path>:/mnt:shared \
  gcsfuse
```

Note: `:shared` is crucial here ... it allows late-mouting in the container be visible to the host filesystem, without it, the host will not see anything as a result that the mounting point is being replaced by the newer one, and thus becoming obsolete.

Note2: the `credential.json` is the **Service Account Key** obtained from **google cloud' api manager** (https://console.cloud.google.com/apis/credentials) in the form of json file.

Note3: `--privileged` mode is there on purpose, without which `fusemount` wouldn't work.

Note4: `/etc/passwd:/etc/passwd:ro` is there becasue we want to mount with YOU as the owner. In this case, you have to run commands (in `entrypoint.sh`) using your privileges, however, it won't succeed without a proper `/etc/passwd` file that in which has your username inscribed. 

Note5: if you run the container in `detached` mode, the only clean way to stop the container is to `kill` it using `kill -S SIGINT` not by `stop`.

Okay, you're asking for a `docker-compose` recipe:

```
version: '2'

services:
    gcsfuse:
        build: .
        environment: 
            - BUCKET_NAME=<your-bucket-name-here>
            - UID=<your-user-id-here>
        volumes:
            - ./credential.json:/credential.json:ro
            - ./mnt:/mnt:shared
            - /etc/passwd:/etc/passwd:ro
        privileged: true
```

At your disposal, run `docker-compose up`

**Warning:** there is a problem when stopping docker-compose using `ctrl+c` or `down`, it will forget to unmount the drive first ! In that case, you have to manually `sudo umount <path>` on the host by yourself. `docker-compose kill -s SIGINT` might be the only **CLEAN** way of stopping the docker-compose.
