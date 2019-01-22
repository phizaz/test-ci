---
layout: post
title: Of Elementary OS
---
### Install Google Chrome

Epiphany is not that responsive ... even with my first 10 minutes of using it, I can easily capture the bad experiences. So, I will go back to the old-chrome.

If in doubt, just go https://www.google.com/chrome/browser/desktop/

```
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
sudo apt install ./google-chrome-stabel_current_amd64.deb
```

### Install Nvidia Drivers

```
# if you don't have the 'add-apt-repository'
apt-get install software-properties-common  
# begin !!
sudo add-apt-repository ppa:graphics-drivers/ppa  
sudo apt update  
# list all your choices
sudo apt search nvidia  
# install driver 367.44 (at the time of writing)
sudo apt install nvidia-367 
```


### Take back the "Additional Driver" App

```
sudo apt install software-properties-gtk
```


### Scaling The Screen
You might want to change the scaling factor of the screen:
```
gsettings set org.gnome.desktop.interface scaling-factor 1
gsettings set org.gnome.desktop.interface text-scaling-factor 1
```
It works to some extent but the scaling factor cannot be of floating point ... that's a shame. I'll find a way to circumvent this.


Just saw this: http://www.droidtronix.com/post/things-to-do-after-installing-elementary-os-loki
