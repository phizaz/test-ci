---
layout: post
title: Python 3.5 Dependency Injection with Strong Type Hint
---
This is my first try ...

```
def plus_function(a: int, b: int) -> int:
    return a + b

class SomeAction(Injectable):
    inject_a: str = 'a'
    inject_plus: plus_function = plus_function

    def call(self):
        print(self.inject_a)
        print(self.inject_plus(10, 20))
```

And `Injectable` is just ...

```
from typing import NamedTuple
Injectable = NamedTuple
```
