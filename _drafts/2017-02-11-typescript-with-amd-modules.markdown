---
layout: post
title: Typescript with AMD Modules
---
Even though `typescript` build system is great it still needs your work. To use amd most certainly you need `requirejs`.

`source/entry.ts`

```
declare var require: any;
require(['assets/js/index.js'])
```

`source/main.ts`

```
// your typescript code here
import ... from ...
...
```

Build config: `tsconfig.json`

```
{
    "compilerOptions": {
        "module": "amd",
        "target": "es5",
        "noImplicitAny": false,
        "sourceMap": true,
        "watch": true,
        "outDir": "web/assets/js"
    }
}
```

`web/index.html`

```
<script src="https://cdnjs.cloudflare.com/ajax/libs/require.js/2.3.2/require.min.js"></script>
<script src="assets/js/entry.js"></script>
```

Enjoy!
