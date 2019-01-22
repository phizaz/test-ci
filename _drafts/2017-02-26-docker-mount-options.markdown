---
layout: post
title: docker mount options
---
docker run -v /mnt:/mnt:{option}

Options
1. `private`
2. `rprivate`
3. `rslave` (recursive)
4. `shared` 

normal mount will not work with `fuse` mount in the container (or vice versa)
