---
layout: post
title: VSCode to Remove \r Automatically
---
Work with compatible mode (\r\n) can be frustrating when you deal with Unix.

`.sh` files don't work with \r newlines (atleast when I call `chown`, it renders path to be invalid) 

To config vscode to use only `\n` is to config:

```
"files.eol": "\n"
```

To make to replace `\r\n` to `\n` is to 

So far, I have no clue how to remove it using VSCode let alone do it automatically. 

The best bet I have now is to use `sed` to replace the `\r\n` with `\n`  and that works, but not good enough for me I think.
