---
layout: post
title: USB Passthrough to an LXC (Proxmox)
categories: dev
tags: vfio proxmox
---

The idea of USB pass through to an LXC container can be done by “mounting” the device inside the container space. However, it doesn’t mean the container can mingle with the device just yet. We also need to “allow” the container to do so as well.

So first we need to locate the device we want to pass through. **lsusb** could be used to find the connecting USB devices.

Note: **lsusb** will not work inside the container because there are no devices there yet

Here is the output of my case:

```
Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 001 Device 013: ID 045e:0800 Microsoft Corp.
Bus 001 Device 020: ID 04a9:1746 Canon, Inc.
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
```

In my case, I want to pass through my Canon MP printer to the container. So, I look specifically at the “Canon, Inc.”

The **bus** and **device** numbers are what we are looking for now, they can guide us to the correct device path beneath `/dev` . In this case, they are **001** and **020**.

So the device path, that we need to mount to the container, should be `/dev/bus/usb/001/020` . How to mount the container with this path? we can do it via the `lxc.mount.entry` option.

But, as I said before, it is not enough to just mount the device path, the container must be allowed to use it as well. We can do it via cgroup, more specifically option `lxc.cgroup.device.allow` . To use this option we need to know the device’s **major** and **minor** numbers which can be retrieved as follows:

```
ls -al /dev/bus/usb/001/020
```

I got:

```
crw-rw-r — 1 root lp 189, 19 Jul 10 17:32 001/020
```

The major and minor numbers are **189** and **19** respectively.

### Start the configuration

In my case, I use Proxmox to manage my containers. I need to go to `/etc/pve/lxc/<container_id>.conf` .

I will have to put two lines more into the configuration file:

```
lxc.cgroup.devices.allow: c 189:* rwm
lxc.mount.entry: /dev/bus/usb/001/020 dev/bus/usb/001/020 none bind,optional,create=file
```

The former is for allowing the container privilege to access the device specified by its major and minor numbers.

Note: **189:\*** means we care only the major number, all the minors apply. **rwm**means read-write-mount.

The latter is for mounting the device file representation into the container space. In this case, I mount the exact device, however, it might be a good idea to map the whole directory (with all its siblings) to the container because it is more than likely that the filename will change.

Given that it can be done thus:

```
lxc.mount.entry: /dev/bus/usb/001 dev/bus/usb/001 none bind,optional,create=dir
```

Note: the target mount path **doesn’t** begin with a slash! it is `dev` not `/dev`

Now, stop and start the container. You should now be able to run the command **lsusb** inside the container and see the same results as if in the host.