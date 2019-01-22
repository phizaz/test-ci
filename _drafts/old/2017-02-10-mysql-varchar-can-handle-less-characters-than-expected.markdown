---
layout: post
title: MySQL varchar can handle less characters than expected
---
```
VARCHAR(30) 
```

with UTF8 encoding (on Mysql 5+) should be able to handle exactly 30 characters, not 30 bytes which might be much less when using not-english characters, esp. Chinees and Thai.

Even, knowing I still fell into the pitfall, I just forgot to declare `SET NAMES UTF8` at the top of my `.sql` file.
