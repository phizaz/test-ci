---
layout: post
title: 'Python Setup Tools : Adding Non-Code Files'
date: 2016-08-28 16:09:05.000000000 +07:00
tags: python
---
*Took from http://python-packaging.readthedocs.io/en/latest/non-code-files.html*

Often packages will need to depend on files which are not .py files: e.g. images, data tables, documentation, etc. Those files need special treatment in order for setuptools to handle them correctly.

The mechanism that provides this is the MANIFEST.in file. This is relatively quite simple: MANIFEST.in is really just a list of relative file paths specifying files or globs to include.:

```
include README.rst
include docs/*.txt
include funniest/data.json
```

In order for these files to be copied at install time to the package’s folder inside site-packages, you’ll need to supply `include_package_data=True` to the setup() function.
