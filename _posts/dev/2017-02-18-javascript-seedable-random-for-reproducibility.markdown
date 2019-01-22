---
layout: post
title: Javascript Seedable Random for reproducibility
date: 2017-02-18 08:28:55.000000000 +07:00
categories: dev
tags: js
---
I would never understand the seed of seedable random functions, if not from the academics background or with testing in mind.

In academics, it is important for the results to be reproducable so that other people could test and review it and have the same results as you do (brag).

With testing in mind, you would find it hard to test functions with random behavior it just doesn't work well with the unit testing and simple assertions. With seedable random functions you can seed it with the same seed during test, and you will get the exact-same-predictable results every time.

Unfortunately, `Math.random()` doesn't support this.

From **Mozilla** (https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/random), I quote:

> The Math.random() function returns a floating-point, pseudo-random number in the range [0, 1) that is, from 0 (inclusive) up to but not including 1 (exclusive), which you can then scale to your desired range. The implementation selects the initial seed to the random number generation algorithm; **it cannot be chosen or reset by the user.**

Luckily, you have an alternative **Math.ramdom()**, here https://github.com/davidbau/seedrandom.

**Usage:** (took from the repo)

```
Math.seedrandom('hello.');
console.log(Math.random());          // Always 0.9282578795792454
console.log(Math.random());          // Always 0.3752569768646784
```
