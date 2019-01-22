---
layout: post
title: Convert Two-Digit Year to Four-Digit Year
date: 2016-09-15 09:07:36.000000000 +07:00
categories: dev
tags: c#
---
In C#.NET, From http://stackoverflow.com/questions/2024273/convert-a-two-digit-year-to-a-four-digit-year:

The .NET framework has a method that does exactly what you want:
```
int fourDigitYear = CultureInfo.CurrentCulture.Calendar.ToFourDigitYear(twoDigitYear)
```

Note : Beware of the date setting on each machine can be vastly different, since this `CultureInfo` is based on the machine setting. The result of this function can be unpredictable, e.g. on machines using Thai locale, BE is a preferred epoch, but it doesn't make any sense with `ToFourDigitYear`.
categories: dev

Workaround : You can set the current thread's `CultureInfo` to something you're sure of the `CultureInfo` behavior and then perform the call. After that return the current thread's `CultureInfo` back to the old one.
