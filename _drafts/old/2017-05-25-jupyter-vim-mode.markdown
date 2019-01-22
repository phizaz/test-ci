---
layout: post
title: Jupyter + VIM Mode
---
https://github.com/lambdalisue/jupyter-vim-binding

### Install IPython Notebook Extensions First !

(https://github.com/ipython-contrib/jupyter_contrib_nbextensions#installation)

Using conda, `conda install -c conda-forge jupyter_contrib_nbextensions`

### Install VIM Binding to IPython Notebook Extensions

(https://github.com/lambdalisue/jupyter-vim-binding/wiki/Installation)

```
# You may need the following to create the directoy
$ mkdir -p $(jupyter --data-dir)/nbextensions
# Now clone the repository
$ cd $(jupyter --data-dir)/nbextensions
$ git clone https://github.com/lambdalisue/jupyter-vim-binding vim_binding
$ chmod -R go-w vim_binding
```
