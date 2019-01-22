---
layout: post
title: Install Intel Graphics Driver for Ubuntu 16.04
---
https://01.org/linuxgraphics/downloads/intel-graphics-update-tool-linux-os-v2.0.2

Stick with v2.0.2 (for newer versions will support Ubuntu 16.10 insted)

Download it and:

```
wget https://download.01.org/gfx/ubuntu/16.04/main/pool/main/i/intel-graphics-update-tool/intel-graphics-update-tool_2.0.2_amd64.deb
sudo dpkg -i ./intel-graphics-update-tool_2.0.2_amd64.deb
```

Now open (from the launcher) the Intel Updater ... it should guide you all the way through.

On using the `apt-get update` you might see something like

```
W: GPG error: https://download.01.org/gfx/ubuntu/16.04/main xenial InRelease: The following signatures couldn't be verified because the public key is not available: NO_PUBKEY 56A3DEF863961D39
W: The repository 'https://download.01.org/gfx/ubuntu/16.04/main xenial InRelease' is not signed.
N: Data from such a repository can't be authenticated and is therefore potentially dangerous to use.
N: See apt-secure(8) manpage for repository creation and user configuration details.
```

That means you might have to explicitly "trust" the key by adding manually this way:

```
apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 56A3DEF863961D39
apt-get update # should work now
```

Please find your own key in your own run. This should now work.
