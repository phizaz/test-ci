---
layout: post
title: 'Python Getting A Free Port Number : A Multiprocess-safe Recipe'
date: 2016-08-03 15:34:15.000000000 +07:00
tags: python
---
Goal: a function `get_free_port() -> port number` that supports multi-thread and multi-process race condition. 

That means you can call `get_free_port()` no matter when and where and you should be able to bind that port number care-free.

Actually, the goal is not *logically* attainable, because `get_free_port()` will have no idea whether ports output from it have done being used or not. So, the best it can do is to remember every port output, and make sure not to use the one in the list.

It should be more logical if we *add* another part to the function which goes `release_port()` or another way that will do this automatically.

We know that calling `socket.bind(('', 0))` will automatically bind a free port for us. In a sense, we can do something like this:
```
import socket 

def get_free_port():
	sock = socket.socket()
	sock.bind(('', 0))
	ip, port = sock.getnameinfo()
	sock.close()
	return port
```

It seems ... working but not quite. Because this approach let the port *unbound* for a short time, and thus subjective to race condition.

So far, we know that getting a free port is not so hard, but transferring a port safely to a caller is *much much* harder.

In thinking about this, we should come up with an idea that the port, being unbound, should first have another mechanism to prevent any mischievous binding. I have come up with something like this:

```
get a free port -> look at dictionary (and lock file) -> bind a free port -> write a dictionary (and lock file) -> release port -> return the port
```

From above, you will see that I invoke the use of `lock file` by which I can be certain that even the port is left unbound it will not be reacquired by any process in any possible race conditions. (I use lib [fasteners](http://fasteners.readthedocs.io) here)

I have translated this into this code (with testing):

<script src="https://gist.github.com/phizaz/c62162f45ca1f453ce265d09222ee96d.js"></script>
