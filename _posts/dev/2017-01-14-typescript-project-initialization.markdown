---
layout: post
title: Typescript Project Initialization
date: 2017-01-14 03:06:20.000000000 +07:00
tags: js typescript
---
Create `tsconfig.json`

```json
{
    "compilerOptions": {
        "target": "es6",
        "module": "commonjs",
        "sourceMap": true,
        "watch": true,
        "allowJs": true,
        "removeComments": true
    }
}
```

Node modules won't be found by Typscript until you install the type descriptions:

```bash
npm install --save-dev @types/node
```

And also you should do this to other javascript modules. 

###### In case you want a stack trace in typescript

Install source map support ... and let it do the work for you

```bash
npm install --save-dev source-map-support
```

To use it:

```js
require('source-map-support').install()
```

In your entry files, I usually put this at the top of my test files. In production code, I don't want an extra `require`.


By the way, it's a good idea to code Typescript using Visual Studio Code because of its great support. 

Here are some addition steps if you're using it

1. Add build task using the `tsconfig.json`
2. Build (typically `ctrl + shift + b`)
3. Code

