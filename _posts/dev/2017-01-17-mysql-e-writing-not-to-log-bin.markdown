---
layout: post
title: Mysql "-e" writing not to log bin
date: 2017-01-17 03:02:24.000000000 +07:00
tags: mysql
---
Since, mysql version 5.7 there is no easy way to turn off the log bin globally. In the other hand, you can stop the log bin locally (during session) using `SET sql_log_bin = 0` but this way the result won't last over sessions. 

Thus, rendering `mysql -u {user} -p{user} -e "...."` much harder to do without writing to the bin log. However, there is another way. Nothing prohibits you from **sandwiching** the sql command to keep it away from the bin log, this way : 

```
mysql -u {user} -p{user} -e "SET sql_log_bin=0; {sql};SET sql_log_bin=1" 
```

By sandwiching the sql, your sql will be run in the context which log bin is not working and you are fine !
