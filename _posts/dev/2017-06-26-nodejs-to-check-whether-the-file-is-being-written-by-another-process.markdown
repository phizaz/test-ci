---
layout: post
title: NodeJS to check whether the file is being written by another process
date: 2017-06-26 08:34:03.000000000 +07:00
categories: dev
tags: js
---
To me at first, it should have a simple graceful solution, but turned out it has none. 

I will try to convince you by the following scenario.

### Writer

I have a python writer (to make sure the file will be written by another process rather than the node itself), here is the code: 

```
import os
import time
from functools import partial

def copy(a, b):
    with open(a, 'rb') as r:
        with open(b, 'wb', 0) as w:
            for chunk in iter(partial(r.read, 4 * 1024), b''):
                w.write(chunk)
                time.sleep(0.1)
```

Basically, it will write a file by copying chunk by chunk of 4 KB to the destination. 

I have added some timeout in the code to make the write slower and to emphasize the problem.

### Reader 

Now, we expect that the reader must be able to know whether the file is being written by the code above.

#### Things that don't work 

1. `fs.access(path, fs.constants.R_OK & fs.constants.W_OK & fs.constants.X_OK, callback)`
2. `fs.open(path, 'r+', callback)`
3. `fs.open(path, 'a+', callback)`
4. `lockfile.lock(path, {}, callback)` by using `npm install lockfile` here. It just cannot lock whether how long.
5. `lockfile.lock(path, callback)` by using `npm install proper-lockfile` here. It always can lock.


#### Things that do work

1. Check the file size difference:
    ```
    const aSize = await fileSize(path)
    await delay(100)
    const bSize = await fileSize(path)
    return aSize !== bSize
    ```
    Note: It is not reliable though because you cannot guarantee that the `delay` you put is enough for any writer.

2. Check whether the file can be moved:
    ```
    const tmp = path + '-tmp'
    try {
        await rename(path, tmp)
        await rename(tmp, path)
        return false
    } catch (err) {
        return true
    }
    ```

#### Conclusion

To what I have to be working, **try-moving-the-file** wins because it is the most reliable !
