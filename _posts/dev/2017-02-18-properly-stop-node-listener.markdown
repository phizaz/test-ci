---
layout: post
title: Properly Stop Node Listener
date: 2017-02-18 08:17:42.000000000 +07:00
categories: dev
tags: js
---
Here is a listener on `process` object:

```
process.on('unhandledRejection', (err) => {
    console.error(err)
})
```

And, a way to remove the listener is:

```
process.removeListenter(<name>, <function>)
```

Note that, you have to specify both its name and function because for each name there can be many functions that listen. 

So, inpractice, I think it is much easier to manage listeners if we use it this way:

```
function listen(obj, name, func) {
    const on = obj['on']
    const removeListener = obj['removeListener']
    if (typeof on !== 'function'
        || typeof removeListener !== 'function') {
        throw new Error('object is not listenable')
    }
    on(name, func)
    return () => { removeListener(name, func) }
}
```

**Usage:**

```
const remover = litsen(process, 'unhandledRejection', (err) => console.error(err))
# to stop listening
remover()
```
