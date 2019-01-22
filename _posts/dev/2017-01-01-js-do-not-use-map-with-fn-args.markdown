---
layout: post
title: 'JS: Do not use .map() with fn(...args)'
date: 2017-01-01 06:30:50.000000000 +07:00
categories: dev
tags: js
---
Okay, you might say **"of course not!"**, well I just didn't know T_T

The definition of `Array.prototype.map()` is, according to [Mozilla](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/map): 

```
var new_array = arr.map(callback[, thisArg])
```

Where `callback` is: `callback(currentValue, index, array)`

Yes! despite the fact that we usually use `[1,2,3].map((x) => ...)`, utterly careless about the 2nd and 3rd arguments. It's still there, javascript just automagically ignores them. 

Note: those who're so familiar with Python might, like I do, feel the map callback function should have only one argument.

Now, when we combine it with javascript's spread syntax hell begins:

```
function p(...words) { 
    return `<p>${words.join(' ')}</p>`
}

const words = [
    'Hello', 'world!', 'it', 'works!'
]
words.map(p)
```

Here is the results:

```
[ '<p>Hello 0 Hello,world!,it,works!</p>',
  '<p>world! 1 Hello,world!,it,works!</p>',
  '<p>it 2 Hello,world!,it,works!</p>',
  '<p>works! 3 Hello,world!,it,works!</p>' ]
```

It just doesn't work as you might expect as I didn't expect this!
