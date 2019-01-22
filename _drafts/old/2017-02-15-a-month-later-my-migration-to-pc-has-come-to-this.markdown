---
layout: post
title: A Month Later My Migration to PC Has Come to This ...
---
## Keymappings

### ALT and CTRL switching

#### Abstract 

I think I should provide some valid points on this, why should I switch the keys ? And here is the thing, `ctrl` is a super-frequently-used keys and not just typing on we actually **hold** on it. What finger you use for holding it ? usually a pinkie ... it's the least strong of all fingers and it's task is quite strong in this sense. I think Macintosh did a perfect choice on selecting what should be the main key combination as it turned out to be `cmd` key. It's hold by the thumb which is the strongest. Knowing this I want to make `ctrl` appears to be at the position of `alt` and vice versa. 

#### First try 
First I used auto hot key to do the job... `alt::ctrl` (and vice versa), but soon enough I realized that the autohotkey key switching is not so **pure** as it is said in the official website. There are so many side effects from changing alt and ctrl key alone, like you cannot hold alt + ctrl at the same time anymore it won't just recognize it.

#### Moving to KeyMapping (registry)

Switching CTRL and ALT from registry settings are more pure maybe the purest you can get. Using the app KeyMapping (quite hard to find a working link nowadays), anyhow, it works and it works well. I'm so happy with its results combined with the proper AutoHotKey settings.

#### Rethink the AutoHotKey settings


##### About the Alt + Tab


##### Run with high process priority
It could be that under high load AutoHotKey works unpredictably... It just doesn't do as it deemed to do.

##### Also with high privileges as well

With the "run as administrator", the process will not be able to capture the keybindings for certail applications, such as the **Task manager**. So, the only way out to my concern is to run it with admistrative privileges.

#### Task Scheduler
To make AutoHotKey starts with the user login, you might try putting its executable into the startup folder, but hey, doing so the start up program will be about the last to be spun up, not good right ? Using **Task Scheduler** instead, you could make it starts as soon as you login rival all the start up services. 

## Of The Shell

### CMDer the shell
I'm still using it .. it's definitely not a perfect alternative to shells in linux or macs, but that about the furthest we can get here on Windows.


### Chocolatey the package manager

It more than just a command line package manager, but it's really a full-fledged package manager meaning it manages everykind of packages including the GUI ones. 

### Linux commands that we all love

Maybe, just maybe, there is a Windows way of doing things nerdly, but I have learned quite a lot of the linux ways and don't want to relearn everything. Why not put these commands that we all love to life on Windows using something like Docker ? 

Note: It's true that most of the useful commonly-used commands are available on Windows natively via the chocolatey, but it's not as pure as the dockerized version itself. What can I expect from the dockerized version of it is so much more than the Windows recompiled version of it.

#### Dockerize everything


##### Dockerize Wget

The dockerfile itself is quit straightforward (https://github.com/phizaz/dockerfiles/tree/master/wget), but the harder part redises much in the integration part, more precisely, how can I use it with ease ?

Usage: `docker run --rm -v {current_dir}:/workdir phizaz/wget {arguments}`

Sure there is a limitation, you should not mount a whole drive `C:` to the container just for the sake of safety alone. So, even though, you still can use the 

##### Alias everything

`cmd.exe` provides a great tool for this. It's called `alias`, it will save you many many key strokes.

Like:

```
alias 
```

##### Make alias dynamic
(I wrote a whole article on this https://blog.konpat.me/a-way-to-set-cmds-alias-with-some-variables-like-cd/)

Anyways, the main idea is.

##### Make `/` and `\` interchangable

Docker runs on a linux. It strictly uses `/`, but on windows it's the otherwise. Everytime you `tab` it will automatically fix your path to be of Windows which clearly won't work on Docker forcing you to type all the way through, not-nice-at-all.

I created a script that will translate `\` into `/` so that even the input is a result of auto path correction by Windows... I'll be just fine.


##### Dockerize Rsync

Even with full docker support on Windows, I have yet to dockerize everykind of commands in linux .. not all of them are possible even in theory, but hey, we have gotten quite far.

## Cloud Drives

**iCloud drive** with the optimized storage feature is hard to beat by any service out there. I have been looking for it alternative on Windows, and heard that once in a while **OneDrive** used to have this feature called **placeholders**, which is said to be removed because it caused so much confusion to the users whether a file is on the cloud or on the machine. I don't really buy to this excuse, I think it's because the placeholder cannot fully act as an actual file yet. To support the placeholder at the same level as the one in  macOS, you need the OS filesystem itself to buy the idea of placeholder not only the program, and sure enough it takes some time for it to materialize. 

You might argue that **odrive** does support this feature, but I will argue that it's not at the filesystem level the placeholder is just a file of other type with other extension such that upon execution (open) it calls the **odrive** executable to fetch the file from the cloud and execute it later on. This process works well given the assumption that you only access files through the **explorer** which is not always the case. And by the way, you have no way to measure the file size without downloading it to the local machine.

So, in the meanwhile, there is no **PERFECT** alternative to the optimized storage of iCloud drive. I hope for it. May Microsoft bring this feature to Windows in the upcoming big updates !

### OneDrive

I have written a whole article about the experiece with OneDrive and why I keep using it here https://blog.konpat.me/my-experiences-with-onedrive-review/
