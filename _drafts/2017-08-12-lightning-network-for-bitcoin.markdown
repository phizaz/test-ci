---
layout: post
title: Lightning Network for Bitcoin
---
Here is the paper: ...

How can it provide almost infinite throughput for the blockchain ? 

Do you know that there is only so much throughput that a public blockchain can support. The nature that "everyone knows everything" will eventually be the bottleneck. 

So, how can we improve a blockchain when it hits its theoretical limit ? 

The answer is "go away from blockchain" or in this case settle transactions outside the blockchain. 

But this cannot be acceptable without a good clarification. Since blockchains were invented to solve the trustability problem of the peer-to-peer nature of the internet, without which the whole system is sure to collapse.

So "we need blockchain" but we don't need it all the time, that is the whole point for lightning.

Imagine if A and B want to do some transactions, of course A has blockchain address of `addr(A)` and same goes for B `addr(B)`.

Say the transaction is `send(10, addr(A), addr(B))`, sending 10 tokens from A to B.

Normally, the transaction will be "broadcasted" throughout the blockchain network, for that "miners" will know and put it into the next block and finally append the blockchain. This is the process which bitcoins and many more are doing today.

On the other hand, lightning suggests that if A and B are doing some rapid transactions it is superfluous to broadcast those transactions in the rapid succession which will greatly reduce the throughput. 

In fact, A and B can come up with some "agreement" so that when both parties, A and B, end up signing the "special transaction" it is only a matter of broadcasting to make that transaction comes into reality. 

Abou

With this agreement, A and B need not rush to broadcast the transaction knowing that the money in transaction can always be claimed without further cooperations.

However, if A and B happen to have many transactions, it might not work. 

Consider this, there are two transactions `send(10, addr(A), addr(B))` and `send(5, addr(A), addr(B))`

The actual transactions to be signed with both parties are `send(10, addr(A), addr(B))` and `send(5, addr(A), addr(B))` instead, because

