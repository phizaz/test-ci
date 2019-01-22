---
layout: post
title: Chocolatey's Rsync installs another ssh that doesn't work
date: 2017-01-14 06:28:39.000000000 +07:00
tags: windows
---
As rsync requires `ssh`, it automatically installs one for you. The problem is its copy of ssh will replace one of yours. 

The worst part is it's a copy of ssh that doesn't work with Windows becasue it looks for the home folder that is not Windows-based like `/home/{username}` instead it should be `C:\Users\{username}`. 

This casues me a lot of frustration util, of course, I removed it, and went back to use Cmder's ssh. 

###### Background

I tried `ssh` to somewhere with verbose mode. You'll see that in this case it's looking for `/home/Ta` (`Ta` is my username) which is very wrong because I'm using Windows here ... and the path should be `/c/Users/Ta/` or so.

```
ssh -v git@github.com
OpenSSH_7.1p1, OpenSSL 1.0.2e 3 Dec 2015
debug1: Connecting to github.com [192.30.253.112] port 22.
debug1: Connection established.
Could not create directory '/home/Ta/.ssh'.
debug1: key_load_public: No such file or directory
debug1: identity file /home/Ta/.ssh/id_rsa type -1
debug1: key_load_public: No such file or directory
debug1: identity file /home/Ta/.ssh/id_rsa-cert type -1
debug1: key_load_public: No such file or directory
debug1: identity file /home/Ta/.ssh/id_dsa type -1
debug1: key_load_public: No such file or directory
debug1: identity file /home/Ta/.ssh/id_dsa-cert type -1
debug1: key_load_public: No such file or directory
debug1: identity file /home/Ta/.ssh/id_ecdsa type -1
debug1: key_load_public: No such file or directory
debug1: identity file /home/Ta/.ssh/id_ecdsa-cert type -1
debug1: key_load_public: No such file or directory
debug1: identity file /home/Ta/.ssh/id_ed25519 type -1
debug1: key_load_public: No such file or directory
debug1: identity file /home/Ta/.ssh/id_ed25519-cert type -1
debug1: Enabling compatibility mode for protocol 2.0
debug1: Local version string SSH-2.0-OpenSSH_7.1
debug1: Remote protocol version 2.0, remote software version libssh-0.7.0
debug1: no match: libssh-0.7.0
debug1: Authenticating to github.com:22 as 'git'
debug1: SSH2_MSG_KEXINIT sent
debug1: SSH2_MSG_KEXINIT received
debug1: kex: server->client chacha20-poly1305@openssh.com <implicit> none
debug1: kex: client->server chacha20-poly1305@openssh.com <implicit> none
debug1: expecting SSH2_MSG_KEX_ECDH_REPLY
debug1: Server host key: ssh-rsa SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8
The authenticity of host 'github.com (192.30.253.112)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no)?
```
