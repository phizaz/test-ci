---
layout: post
title: You should handle timeout in all the IO callbacks in NodeJS
date: 2017-06-29 04:30:49.000000000 +07:00
categories: dev
tags: js
---
At first, you might hope that just calls the function and let it tell you just when the job is done and you wait ... and wait. Soon, you realize that the callback you have been waiting will never come.

This is true, and I can pretty much guarantee you that. I have been working with a project involves in heavy IO operations. Even I try to fine tune the speed at which node reads. It is not suffice as I have seen. It soon slows down and stops completely with the cause I have yet to find. 

So, I decided that I have to put some timeout, which is not provided by default means, here is my attempt: 

```
export async function delay(ms: number) {
    return new Promise<void>((res, rej) => {
        setTimeout(() => res(), ms)
    })
}

class Timeout extends Error {}
export async function delayedError(timeout: number) {
    await delay(timeout)
    throw new Timeout()
}

export async function withTimeout<T>(promise: Promise<T>, timeout: number) {
    return await Promise.race<T>([
        promise,
        delayedError(timeout) as any
    ])
}
```

Let's further say that wrap the `fs.readFile` with this `async`:

```
import fs = require('fs')

export async function readFile(path: string) {
    return new Promise<Buffer>((res, rej) => {
        fs.readFile(path, (err, content) => {
            if (err) rej(err)
            else res(content)
        })
    })
}
```

To use it, now you can:

```
try {
    await withTimeout(readFile('test.txt'), 1000)
} catch (err) {
    if (err instanceof Timeout) {
        console.error('timeout')
    } else {
        throw err
    }
}
```

