---
layout: post
title: Add Windows 10's Mail to Startup Programs
date: 2017-01-25 13:49:08.000000000 +07:00
tags: windows
---
It turns out to be more obscure that I expected just to make the Windows' Mail app starts when I log in. I looked its settings I found none that indicates "starts when the user logs in" or so.

Here, we have to do in a hard way.

```
#RUN
shell:AppsFolder
```

Find the mail app, and then create the "Mail" shortcut on your desktop. (It will say that it's not possible to create shortcut there will you create on the desktop instead ? just say yes)

Actually, you could find all sorts of applications here, it's particulary useful with App store apps.

```
#RUN
shell:startup
```

Move the mail shortcut to the startup folder.

You're all set!
