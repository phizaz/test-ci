---
layout: post
title: Babun + HyperTerm
---
> chocolatey + babun + hyerterm

chocolatey for installing windows specific programs

babun = shell + linux package manager

hyperterm = terminal emulator

### Settings in HyperTerm

```
    shell: 'C:\\Users\\<username>\\.babun\\cygwin\\bin\\zsh.exe',
    shellArgs: ['--login'],
    env: {
      CHERE_INVOKING: 1
    },
```

Restart the HyperTerm you will expect to see sub "glitches" on the terminal ... which should be mitigated by `unsetopt PROMPT_SP` at the bottom of your `.zshrc` (in case you don't know where you have to access it through babun's shell, it has a separate user home for your account)

### However, it doesn't live up to the hype

First, it's not fast ... the typing delay, for me, is a very important thing. HyperTerm doesn't seem to provide me this.

Second, HyperTerm problems with displaying characters (like I mentioned above) happens on "every" machine I ssh-ed to, which I can no longer fix for it.

Let's get back to the **cmder** anyways.
