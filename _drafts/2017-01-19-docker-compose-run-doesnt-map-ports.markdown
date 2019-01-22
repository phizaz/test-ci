---
layout: post
title: Docker-compose run doesn't map ports!
---
It just doesn't this is by design ... 

You still can use `up` instead, or `--service-ports` like:

```
docker-compose run --service-ports <name>
```
