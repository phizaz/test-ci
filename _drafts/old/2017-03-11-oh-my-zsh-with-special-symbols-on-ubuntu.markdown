---
layout: post
title: oh-my-zsh with special symbols on ubuntu
---
Install the zsh

```
sudo apt install zsh
```

Install oh-my-zsh

```
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

To use the themes like `agnoster` with fancy special symbols, you have to install the **powerline plugin**: http://askubuntu.com/questions/283908/how-can-i-install-and-use-powerline-plugin. Which can be serialized here:

Install python-pip:

```
sudo apt install python-pip
pip install --upgrade pip
```

Install the powerline:

```
sudo su -c 'pip install git+git://github.com/Lokaltog/powerline'
```

Install the symbols:

```
wget https://github.com/Lokaltog/powerline/raw/develop/font/PowerlineSymbols.otf https://github.com/Lokaltog/powerline/raw/develop/font/10-powerline-symbols.conf
mkdir -p ~/.fonts/ && mv PowerlineSymbols.otf ~/.fonts/
fc-cache -vf ~/.fonts
mkdir -p ~/.config/fontconfig/conf.d/ && mv 10-powerline-symbols.conf ~/.config/fontconfig/conf.d/
```

Put this content to your `~/.zshrc` file to load it automatically: 

```
if [[ -r ~/.local/lib/python2.7/site-packages/powerline/bindings/zsh/powerline.zsh ]]; then
    source ~/.local/lib/python2.7/site-packages/powerline/bindings/zsh/powerline.zsh
fi
```

You might want to restart your terminal.
