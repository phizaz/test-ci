---
layout: post
title: Switch ALT and CTRL in Ubuntu
---
http://askubuntu.com/questions/93624/how-do-i-swap-left-ctrl-with-left-alt-on-my-keyboard

I would sum it up here, we'll use the **xmodmap** app:

First, create a config file, may be `~/.Xmodmap`:

```
vim ~/.Xmodmap
```

Now, insert it with this content:

```
clear control
clear mod1t
keycode 37 = Alt_L Meta_L
keycode 64 = Control_L
add control = Control_L Control_R
add mod1 = Alt_L Meta_L
```

I personally don't know what doesn't it really do, but you should make sure that the `keycode` is correct for your machine, using `xev`.

After you have saved the file, you might want to see the effect:

```
xmodmap ~/.Xmodmap
```

Alt and Ctrl has been switched!

#### Start Xmodmap at Login

```
vim ~/.xinitrc
```

In which, insert this line:

```
xmodmap ~/.Xmodmap
```

No, still no luck!!! why how ? 
