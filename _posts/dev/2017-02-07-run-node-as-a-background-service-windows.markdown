---
layout: post
title: Run Node as a Background Service and Starts on Startup (Windows)
date: 2017-02-07 16:02:36.000000000 +07:00
categories: dev
tags: js
---
Ever wonder how to make your node application works as a background process and has a nice easy to use management interface ? 

Wouldn't be great if you can register your node app as a one of the **Services** in Windows ? 

There is a package just for you ! `node-windows`, just install it right now !

I prefer the local installation way, which is `npm install --save node-windows`, note that in the official instalation they will guite you to install with `-g` flag.

Now, as a forerunner in this, I will kinda guide you through this ... which is quite easy though...

Let's assume that your service can be run in this fashion `node index.js arg1 arg2`, it's reasonable to assume that your program have some arguments.

As far as I know, I found not a way to include arguments in the usage of `node-windows`, may be I just didn't look thorough enough, in the source code. However, in this example I will assume that we have a file (or files) `run.ts` that will run `node index.js args1 args2` for us.

You can imagine `run.ts` to look like.

```
import child_process = require('child_process')
child_process.execSync('node index.js arg1 arg2')
```

So far so good right ? It's kinda work but not exactly favorable ... one thing you have to make sure is that whenever the `run.ts` got **killed**, esp. by `SIGINT` (ctrl + c) which is the way Windows Services manager will use to **stop** your service, the `run.ts` will always relay this signal to its child. 

I think `execSync` is not preferable anymore considering this matter. I rather use `spawn` instead.

```
import child_process = require('child_process')
function signal(proc, signal, cb: (code, signal) => void) {
    proc.on('close', (code, signal) => {
        cb(code, signal)
    })
    proc.kill(signal)
}
export function wrap(cmd: string, args: string[]) {
    const proc = child_process.spawn('node', args)

    // listen on normal error
    proc.on('exit', () => {
        process.exit()
    })

    process.on('SIGINT', () => {
        console.log('SIGINT signal')
        signal(proc, 'SIGINT', (code, signal) => {
            console.log('gracefully closed!')
            process.exit()
        })
    })

    return {
        ...proc,
    }
}

wrap('node', ['index.js', 'arg1', 'arg2']) // wrap and run the code
```

It's bloating I understand, but if you have more creative ideas I will be willing to listen :D

Now, I'll create a file say to be the **service control center**, which we should be able to do the following actions: install, start, stop, uninstall service.

Let's give it a name `windows.ts` (.js if you work in plain javascript), and if we run it like `node windows.js install` it should register the service in the Windows' Service registry.

Let's start small only with **install** capability.

```
import nodeWin = require('node-windows')
const {Service} = nodeWin
import minimist = require('minimist')

function parseArgs() {
    const args = minimist(process.argv.slice(2))
    const [mode] = args['_']
    return { mode }
}

async function install({name, description, script}: { name: string, description: string, script: string }) {
    return new Promise<boolean>((res, rej) => {
        console.log('installing service', name)
        const svc = new Service({ name, description, script })
        svc.on('install', () => {
            console.log('service is installed ...')
            res(false)
        });
        svc.on('alreadyinstall', () => {
            console.log('service is already installed ...')
            res(true)
        })
        svc.on('invalidinstallation', (err) => {
            console.error('invalid installation')
            rej(err)
        })
        svc.on('error', (err) => {
            rej(err)
        })
        svc.install()
    })
}

async function main() {
    const {mode} = parseArgs()
    if (mode === 'install') {
        await install({
            name: "my service",
            description: "it's awesome!!",
            script: "run.js"
        })
    }
}
```

Now, try ... `node windows.js install`. There will be some pop-up requesting for your admin  privilege to install services, to suppress all these annoying you should run the script an elevated cmd.

Let's see a little further how to start, stop, and uninstall the service :

```
async function uninstall({name, description, script}: { name: string, description: string, script: string }) {
    return new Promise<void>((res, rej) => {
        console.log('uninistalling service', name)
        const svc = new Service({ name, description, script })
        svc.on('uninstall', () => {
            res()
        });
        svc.on('error', (err) => {
            rej(err)
        })
        svc.uninstall()
    })
}

async function start({name, description, script}: { name: string, description: string, script: string }) {
    return new Promise<void>((res, rej) => {
        console.log('starting service', name)
        const svc = new Service({ name, description, script })
        svc.on('start', () => {
            res()
        });
        svc.on('error', (err) => {
            rej(err)
        })
        svc.start()
    })
}

async function stop({name, description, script}: { name: string, description: string, script: string }) {
    return new Promise<void>((res, rej) => {
        console.log('stopping service', name)
        const svc = new Service({ name, description, script })
        svc.on('stop', () => {
            res()
        })
        svc.on('error', (err) => {
            rej(err)
        })
        svc.stop()
    })
}
```

Putting things togther is quite straightforward ... at least now you have a clue how to do it.

By the way, the installed service will be run automatically after the computer starts, quite neat indeed.

