---
layout: post
title: Pushing Python Project to Pypi using Twine
date: 2017-03-30 07:36:02.000000000 +07:00
tags: python
---
(thread: https://github.com/pypa/twine#why-should-i-use-this)

You should now prefer `wheel` instead of `egg` why ? (https://packaging.python.org/wheel_egg/)

And also, with the new [pypi.io](http://pypi.io), you don't really need to register your package, just go to the upload step. (https://github.com/pypa/twine/issues/200)

### Build

```
pip install wheel
python setup.py sdist bdist_wheel # building wheel
```

### Register (if needed; not needed for pypi.io) 

```
twine register dist/...
```

### Upload 

```
twine upload dist/...<file .whl>
```

