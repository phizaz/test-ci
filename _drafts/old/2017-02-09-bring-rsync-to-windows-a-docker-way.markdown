---
layout: post
title: Bring Rsync to Windows A Docker Way
---
Needless to introduce what **rsync** is....

```
docker pull phizaz/rsync
```

You can now run rsync using this command: 

```
docker run --rm -it -v %HOME%\.ssh:/ssh:ro -v %CD%:/workdir phizaz/rsync --help 
```

But hey, it's faaaar too long, right ? Let's shorten it, using `alias`

I have written a blong on how to bring %cd% and %home% to `alias` [here](https://blog.konpat.me/a-way-to-set-cmds-alias-with-some-variables-like-cd/). You should go and see that it's not obvious as you might think.

