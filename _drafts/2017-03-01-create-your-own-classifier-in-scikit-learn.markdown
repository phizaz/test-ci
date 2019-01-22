---
layout: post
title: Create your own classifier in scikit-learn
---
http://danielhnyk.cz/creating-your-own-estimator-scikit-learn/

```
class Cls(BaseEstimator, ClassifierMixin):

    def __init__(self, a):
        self.a = a # must be of the same name !! if not, it will fail somewhere
```
