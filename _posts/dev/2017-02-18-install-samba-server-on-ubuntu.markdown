---
layout: post
title: Install Samba Server on Ubuntu
date: 2017-02-18 07:58:27.000000000 +07:00
categories: dev
tags: ubuntu
---
(thread: https://help.ubuntu.com/community/How%20to%20Create%20a%20Network%20Share%20Via%20Samba%20Via%20CLI%20(Command-line%20interface/Linux%20Terminal)%20-%20Uncomplicated,%20Simple%20and%20Brief%20Way!)

```
sudo apt-get update
sudo apt-get install samba
sudo smbpasswd -a <user_name>
# backup 
sudo cp /etc/samba/smb.conf ~
sudo vim /etc/samba/smb.conf
```

Add this at the end of file (shortcut `G`):

```
[<folder_name>]
path = <full_path_to_shared_folder>
valid users = <user_name>
read only = no
```

```
# restart to take effect
sudo service smbd restart
```
