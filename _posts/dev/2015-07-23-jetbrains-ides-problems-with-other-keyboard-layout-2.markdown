---
layout: post
title: Jetbrains' IDEs Problems with Other Keyboard Layout
date: 2015-07-23 11:06:00.000000000 +07:00
categories: dev
tags: dev
---
***Update: It seems this process doesn't work anymore, I'm currently using Karabiner-trick instead***
categories: dev

When I started to use PyCharm CE (Community Edition; Free) other than finding that it is a really good IDE, personally it's better than using my favorite text editor, I found out that it had problems with non-QWERTY keyboard layouts as well. Since I'm using the Dvorak keyboard layout, (something that's not QWERTY) I was in a trouble.

The problem is that, normally, every JetBrains' IDEs have problems with key-binding if you're not using the QWERTY keyboard layout. The symtom is it mixes up the QWERTY's key-binding with your layout's key-binding. **This will surely confuse you!**

I did a lot of research, (something like 30 minutes googling) and finally found something that can compensate this. Note that it *doesn't* perfectly solve this problem, *they said*, but for me it *does* solve my problem very well.

The solution is here: ([original post](https://youtrack.jetbrains.com/issue/IDEA-63779#comment=27-841611))

In short, you just have to make change to your registry settings like this: (because it's not enabled by default)

```
ide.non.english.keyboard.layout.fix=true
```

However, it's not that easy to do so. (it's something more advanced settings) You have to follow [this link](https://stackoverflow.com/questions/11829434/local-history-configuration-in-intellij-idea-11/11829639#11829639) to gain access to that settings.

I have *copied* it here:

Another way is to use `Help` | `Find Action`, type **Registry** in the box, ensure that `Include non-menu actions` option is enabled, select the **Registry** result from the list, press **Enter**
