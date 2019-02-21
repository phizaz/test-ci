---
layout: post
title: KVM GPU Passthrough Finding The Right Bios for Your Nvidia Pascal GPU
categories: dev
tags: vfio
---

*Disclaimer: I’m new to the KVM myself. Spending hours, I realized that I should write something down. May in the future I stumbled upon the same problem I would have hope to solve it.*

Before the start, here is the brief:

- You want to create a KVM virtual machine for Windows
- You want to bypass (pass-through) the PCI-express port of your GPU to it
- Your GPU is Nvidia Pascal (GTX 10xx)
- You use UEFI mode with your virtual machine
- You have problem with the GPU Bios
- Most likely you get error code 43, or the Windows turned black, but the virtual machine still can boot
- You seem to see that your [GPU is not UEFI compatible ](https://pve.proxmox.com/wiki/Pci_passthrough#How_to_known_if_card_is_UEFI_.28ovmf.29_compatible)?
- But, from the [techpowerup](https://www.techpowerup.com/vgabios/) Bios collection page (or from [nvflash](https://forum.hiveos.farm/discussion/772/guide-nvidia-gpu-vbios-flashing-with-hiveos) software) states that your GPU should support UEFI.
- You use Proxmox, this is not the deal breaker though most of the article doesn’t need it

#### Your GPU is still UEFI compatible!

Don’t believe the [check](https://pve.proxmox.com/wiki/Pci_passthrough#How_to_known_if_card_is_UEFI_.28ovmf.29_compatible) just yet, check what your GPU model (and brand) in the [techpowerup](https://www.techpowerup.com/vgabios/) tells. If it says compatible, it means compatible.

There are ways for the manual check to go wrong. One of them, as I know, is that your GPU Bios is changed during the boot process, this only happens, seemingly, to the first GPU slot.

Because your GPU’s Bios is not right, it’s been changed (why? I don’t fully understand it myself), passing this Bios to the virtual machine won’t work. You need a working Bios with UEFI compatible anyway.

Most of the internet will say, “I will put my card in the second slot then!”. The idea is to avoid the motherboard (or whatever) to mingle with the GPU Bios. So that, we can at least “dump” the correct looking of the Bios, saved as a file. As when we swapped the card back to its first slot, even though the Bios will still be invalidated, but we can supply the virtual machine with the correct one that we have saved.

Well that sounds right but impractical.

#### Techpowerup (or Nvflash) Bios is different from the one you would need to supply to the virtual machine

You cannot just use the download version and supply it to the virtual machine, that wouldn’t work.

Fortunately, there is a certain someone figured this out for us. He figured out that though there are differences, but they are not much apart. It is possible to modify the downloaded Bios to get what we want! He’s made a python script that can convert the Bios from techpowerup for us.

[Here is it: https://github.com/Matoking/NVIDIA-vBIOS-VFIO-Patcher](https://github.com/Matoking/NVIDIA-vBIOS-VFIO-Patcher)

It is a python script and can be used this way:

```
python nvidia_vbios_vfio_patcher.py -i <ORIGINAL_ROM> -o <PATCHED_ROM>
```

The patched bios can be supplied readily to the virtual machine, using the guide from Proxmox wiki[, How to supply the Bios to a virtual machine in Proxmox?](https://pve.proxmox.com/wiki/Pci_passthrough#romfile)