---
layout: post
title: Disable Middle Click on Ubuntu
---
http://askubuntu.com/questions/597064/disabling-middle-mouse-button

It's super annoying that this feature is enabled which touchpad devices, it should not. Now, we'll disable this feature.

```
xinput list
```

To see the list of your mouse devices. In my case, I got this:

```
⎡ Virtual core pointer                    	id=2	[master pointer  (3)]
⎜   ↳ Virtual core XTEST pointer              	id=4	[slave  pointer  (2)]
⎜   ↳ SynPS/2 Synaptics TouchPad              	id=12	[slave  pointer  (2)]
⎣ Virtual core keyboard                   	id=3	[master keyboard (2)]
    ↳ Virtual core XTEST keyboard             	id=5	[slave  keyboard (3)]
    ↳ Power Button                            	id=6	[slave  keyboard (3)]
    ↳ Video Bus                               	id=7	[slave  keyboard (3)]
    ↳ Sleep Button                            	id=8	[slave  keyboard (3)]
    ↳ EasyCamera                              	id=9	[slave  keyboard (3)]
    ↳ Ideapad extra buttons                   	id=10	[slave  keyboard (3)]
    ↳ AT Translated Set 2 keyboard            	id=11	[slave  keyboard (3)]
```

You can see that the touchpad I have is the `id=12`.

I will disable the middle button by excuting this command:

```
xinput set-button-map 12 1 0 3
```

Which should mean something like this "for device id=12, set left-button to 1 (left-click), middle-button to 0 (do-nothing), and right-button to 3 (right-click)".

Make it persist after you reboot: http://askubuntu.com/questions/20298/how-to-make-xinput-settings-persist-after-devices-are-unplugged-replugged-and

```
vim ~/.xsesionrc
```

```
xinput set-button-map 12 1 0 3 kkj
```
