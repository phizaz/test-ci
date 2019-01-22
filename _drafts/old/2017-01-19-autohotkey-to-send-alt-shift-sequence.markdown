---
layout: post
title: AutoHotKey to Send Alt +Shift Sequence
---
You cannot just `Send, {Alt}{Shift}` and expect them to work as a input language switching sequence. It just won't work. 

The actual sequence you need to send is here:

```
Send, {Alt Down}{Shift Down}{Shift Up}{Alt Up}
```

Works like a charm....

However, you cannot apply this trick to send `Ctrl+Shift` as a keyboard layout switching sequence in Window. (I still don't know how to do it though)
