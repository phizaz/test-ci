---
layout: post
title: Config Required Required Node Version in Package.json
---
From: https://docs.npmjs.com/files/package.json#engines

> engines

> You can specify the version of node that your stuff works on:

>> { "engines" : { "node" : ">=0.10.3 <0.12" } }

> And, like with dependencies, if you don't specify the version (or if you specify "*" as the version), then any version of node will do.

> If you specify an "engines" field, then npm will require that "node" be somewhere on that list. If "engines" is omitted, then npm will just assume that it works on node.

> You can also use the "engines" field to specify which versions of npm are capable of properly installing your program. For example:

>> { "engines" : { "npm" : "~1.0.20" } }
> Note that, unless the user has set the engine-strict config flag, this field is advisory only.
