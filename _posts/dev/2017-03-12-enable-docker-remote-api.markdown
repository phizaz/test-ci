---
layout: post
title: Enable Docker Remote API
date: 2017-03-12 06:47:28.000000000 +07:00
tags: docker
---
Do you know that `docker` doesn't allow remote API as default. So, if you want to use the remote interpreter feature of PyCharm, you are out of luck out of the box.

In which case, you have to **enable** it first... here is how you do it.

the original article  is [here](https://success.docker.com/KBase/Using_systemd_to_Control_the_Docker_Daemon)

First thing, let's see the docker status first: 

```
sudo systemctl status docker
```

```
● docker.service - Docker Application Container Engine
   Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
  Drop-In: /etc/systemd/system/docker.service.d
           └─override.conf
   Active: active (running) since จ. 2017-02-27 14:10:48 ICT; 4min 16s ago
     Docs: https://docs.docker.com
 Main PID: 10750 (dockerd)
   CGroup: /system.slice/docker.service
           ├─10750 /usr/bin/dockerd -H fd://
           └─10768 docker-containerd -l unix:///var/run/docker/libcontainerd/docker-containerd.sock --metrics-interval=0 --start-timeout 2m --state-dir /var/run/docker/libcontainerd/containerd --shim docker-cont

... so much more ..
```

In CGroup, you will see that the `dockerd` is run with almost no arguments. 

We might expect it to be like this instead to allow the **local** connection to the `dockerd` via a tcp port:

```
***** dockerd -H tcp://127.0.0.1:2375 -H unix:///var/run/docker.sock
```

To realize that, we have to edit how the `dockerd` is started:

```
sudo systemctl edit docker
```

Will open an editor editing a specific newly generated configuration file, which by the way should be blank in this case.

Add these lines:

```
[Service]
ExecStart=
ExecStart=/usr/bin/docker daemon -H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock
```

To take effect, restart the daemon:

```
sudo service docker restart
```
