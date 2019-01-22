---
layout: post
title: A Python File Should Contain "main()" Function
date: 2016-11-12 14:42:41.000000000 +07:00
tags: python
---
I think I found a recipe for a new Python file:

```
def ...(): ...
def ...(): ...

def main():
    ...
    ...

if __name__ == '__main__':
    main()
```

**Instead** of putting everything in `main()` plainly under the `if` statement. 

###### Why ? I have a list:

1. You should not be able to create and mutate global vars at will and during runtime! Under the `if` statement, any var declared will be global.

2. It just harder for IDE to help us auto-suggesting the code. The IDE will not know whether a var or a function declared under the `if` statement will be available at the runtime or not. This is a bad thing because the auto-suggestion list will contain a bunch of choices that doesn't work.

3. And, it's also easier for unit-testing. Instead of indirectly running a `.py` file, we can direct this into just using a function call.
