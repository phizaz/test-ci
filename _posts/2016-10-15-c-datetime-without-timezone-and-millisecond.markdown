---
layout: post
title: C# DateTime without Timezone and Millisecond
date: 2016-10-15 09:35:13.000000000 +07:00
tags: c#
---
##### Without Millisecond
From http://stackoverflow.com/questions/1004698/how-to-truncate-milliseconds-off-of-a-net-datetime:

```
dateTime = dateTime.AddTicks( - (dateTime.Ticks % TimeSpan.TicksPerSecond));
```

##### Without Timezone
From http://stackoverflow.com/questions/3188933/prevent-timezone-conversion-on-deserialization-of-datetime-value:
```
DateTime dateTime = DateTime.SpecifyKind(DateTime.Now, DateTimeKind.Unspecified);
```
