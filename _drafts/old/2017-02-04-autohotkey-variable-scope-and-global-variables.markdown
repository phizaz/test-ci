---
layout: post
title: AutoHotKey variable scope and global variables
---
If you declare you vars outside a function, like this:

```
a = 10

Function() {
... you cannot access a from here ...
}

... but you can access a from here ...
```

Inside `Function` you cannot access `a`, but you can access it outside the `Function`. This confuses me at first because every computer language I have been familiar with adopts the same scope concept which is not the case for this language.

To make `Function` able to see the `a` you have to declare it to be `global` like this:

```
global a = 10

Function() {
... can access a from here now ...
}

... also here ...
```

So, I would conclude that the **outside** has its own separate scope, as well as inside the function.

The only way to share them is to use the globals.
