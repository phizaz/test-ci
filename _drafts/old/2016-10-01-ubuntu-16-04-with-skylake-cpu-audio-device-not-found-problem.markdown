---
layout: post
title: Ubuntu 16.04 with Skylake CPU Audio Device Not Found Problem
---
The closest thread is here (on stackoverflow) : 
http://askubuntu.com/questions/672187/no-audio-output-devices-detected-in-new-computer-build-with-skylake-z170-mothe

I will try this: 
sudo apt-add-repository ppa:ubuntu-audio-dev/alsa-daily
sudo apt-get update
sudo apt-get install oem-audio-hda-daily-lts-vivid-dkms

Can't find the package

Manually find the package online at https://code.launchpad.net/~ubuntu-audio-dev/+archive/ubuntu/alsa-daily/+packages

There's one requesting the solution : http://askubuntu.com/questions/813384/no-sound-on-ubuntu-16-04-with-intel-skylake-chipset
