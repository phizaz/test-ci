---
layout: post
title: Javascript RegEx to tokenize command line commands
date: 2017-02-02 07:11:40.000000000 +07:00
tags: js
---
Regex: 

```
/'[^']+'|"[^"]+"|[^\s"]+/g
```

So, in javascript (typescript): 

```
function tokenize(command: string): string[] {
    return command.match(/'[^']+'|"[^"]+"|[^\s"]+/g)
}
```

Note: `/g` is global match

If the command line is as follows: 

```
test 'parsing this string' -e yeah "wow wow"
```

Using the code above will give us this:

```
test
'parsing this string'
-e
yeah
"wow wow"
```
