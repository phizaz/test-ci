---
layout: post
title: Python Rebinding  A Socket
date: 2016-08-05 08:50:27.000000000 +07:00
tags: python
---
For `socket`, use: 

```
import socket

sock = socket.socket()
sock.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
sock.bind(('', 5555))
sock.close()
```

For `socketserver`, use:
```
import socketserver

socketserver.TCPServer.allow_reuse_address = True
... do stuff ...
```
