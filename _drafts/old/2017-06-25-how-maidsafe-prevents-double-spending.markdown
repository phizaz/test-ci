---
layout: post
title: How MaidSafe prevents double spending
---
https://safenetforum.org/t/prevent-double-spend-without-close-group-consensus/12956/11

If you have a background with **blockchain** and **Bitcoin** you would be convinced that **high-volume**, **low-latency** consensus algorithm using blockchain is not quite possible.

As Bitcoin is still facing today, it struggles to crunch transaction into its tiny 1MB block.


First thing, **MaidSafe** does not utilize any blockchain !

Blockchain, by its very inception, is only designed to be way to make consensus possible and safe. But, by no means, it is the only way to reach consensus !

I was so dumb to think that it is the only way and any "coins" must utilize it.

**MaidSafe** simply sacrifices the ability "trace" the coin leaving it only ability "verify" its owner. And then, the blockchain itself is now substitute-able !

If any coin is inscribed its owner's name, to change its owner any "manager" must check the name of the one how asks to change (if one has privilege to do so), and then change it ! 

Now, it is not very reliable if we have only one "manager", because anyone is by no means trustworthy, so we will need more "manager" and let them make a single "consensus" that is trustworthy !

So, we have something called **Close group** which comprises of many managers that are closest to us ! Let them talk to each other and then form a single consensus !

But wait ! this system will only work if "majority" of them are good people right ? How come we choose only the one closest to us ? It does not make any sense ... I can simply create many dummy computers pretending to be such a group of managers and then I can simply do anything I can ! 

So, the way to form a close group must be changed ... if I'm not very much mistaken ... It selects from disjoint nodes ... much better now !

![](https://pictr.com/images/2017/06/25/ZGGVj.png)
Ref: https://safenetforum.org/t/prevent-double-spend-without-close-group-consensus/12956/11


I might have misunderstood altogether, have to reread and research it ...
