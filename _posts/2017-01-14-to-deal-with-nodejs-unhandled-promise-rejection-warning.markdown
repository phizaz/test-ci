---
layout: post
title: To Deal With NodeJS Unhandled Promise Rejection Warning
date: 2017-01-14 03:10:03.000000000 +07:00
tags: js
---
```
UnhandledPromiseRejectionWarning: Unhandled promise rejection (rejection id: 2):
```

This happens when you have promise but forget to `.catch()` it.

In my case, I tend to forget this with `async` functions, like:

```
async function test() {
    ....
}

test() // this can cause the unhandled promise rejection
```

You can do it globally this way.

```
process.on('unhandledRejection', (err) => { ... })
```

I don't think it's a good idea to rely on this `process.on(..)`, becasue you have to make sure that you `.then().catch()` your promises correctly in the first place.
