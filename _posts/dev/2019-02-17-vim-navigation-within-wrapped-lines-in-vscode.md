---
layout: post
title: Vim Navigation within Wrapped Lines in VSCode
categories: dev
tags: vscode
---

Noticing that long lines might be soft-wrapped automatically, and navigating with `j` `k` would result in jumping across many soft-wrapped lines (but it is in fact a single line). VIM uses `g j` and `g k` to navigate within the lines instead. 

If you want `j` `k` to be your default navigating keys through the block, one possible way is to remap it to `g j` `g k` instead. As mentioned in this [Stackoverflow thread](https://stackoverflow.com/questions/46433500/vs-code-navigate-within-a-wrapped-line-vs-jumping-to-next-line).

The key mapping for Vim extension for VSCode is done thus:

```
"vim.normalModeKeyBindingsNonRecursive": [
    {
        "before": [
            "j"
        ],
        "after": [
            "g",
            "j"
        ]
    },
    {
        "before": [
            "k"
        ],
        "after": [
            "g",
            "k"
        ]
    }
],
```

Put it in your `settings.json` .