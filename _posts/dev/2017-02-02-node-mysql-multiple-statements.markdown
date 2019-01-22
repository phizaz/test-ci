---
layout: post
title: Node Mysql Multiple Statements
date: 2017-02-02 16:15:32.000000000 +07:00
tags: js mysql
---
If you happen to use `npm install mysql` or the native mysql connetion libary for node. You might encounter with my problem.

Something like, **sql syntax error** with queries containing `;` (semi-colon), to join many quries to one request. I didn't know that this is forbidden by default and I need to specifically enable it with a flag. 

Here is the flag:

```js
var connection = mysql.createConnection({multipleStatements: true})
```

Maybe it is default to prevent the sql injection ? may be... ? 
