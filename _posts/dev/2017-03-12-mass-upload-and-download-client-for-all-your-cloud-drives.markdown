---
layout: post
title: Mass upload and download client for all your cloud drives
date: 2017-03-12 07:31:14.000000000 +07:00
tags: cloud
---
Not all platforms are supported with native clients, even the simple tasks of downloand and uploading (not even syncing) ... I have gone so far as buliding my own download and upload client with the cloud drive of choice, but to make it reliable one must be very resourceful to do so.

I have found `rclone` (rsync for cloud drive; [link](http://rclone.org/)) app just recently, it worked just like I always wanted to do, simple upload and download a lot of files.

Just download, you might put it at `/usr/local/bin`

```
rclone config
```

Now, to upload

```
rclone copy <local_path> <remote_name>:<remote_path>
```

If you want to upload to the `root` at remote, you can just leave the <remote_path> blank.

To download, you simple swap the two

```
rclone copy <remote_name>:<remote_path> <local_path>
```

It will try to skip making changes to the existing files, mostly by the file size.
