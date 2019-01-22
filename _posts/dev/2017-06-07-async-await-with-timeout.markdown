---
layout: post
title: Async Await with Timeout
date: 2017-06-07 08:11:10.000000000 +07:00
tags: js
---
It's not trivial to put a timeout on an async process.

But, here is how you do it.

Let's say we have some `delay` function defined like this one:

```
async function delay(ms) {
    return new Promise((res, rej) => {
        setTimeout(() => {
            res()
        }, ms)
    })
}
```

It's quite a handy function, considering using it like `await delay(1000)`

And extend this function to get a timeout function like this:

```
async function timeout(ms) {
    await delay(ms)
    throw new Error('timeout')
}
```

Suppose you have some long running function around which you want to have a timeout:

```
async function longRunning() {
    ...
}
```

Obviously, you will call it this way `await longRunning()`.

Now, you can put a timeout around it using `Promise.race`, which is a handy function to get only the one that resolved earliest, this way:

```
const res = await Promise.race([
    longRunning(),
    timeout(1000)
])
```

You now have it ! In a normal case, `longRunning()` is expected to be resolved earlier than `timeout(1000)` and the `const res` is the return value of that `longRunning()` function, but when the `timeout(1000)` is resolved earlier, it throws causing `await Proimse.race` to throw as well, no return value recorded. 

