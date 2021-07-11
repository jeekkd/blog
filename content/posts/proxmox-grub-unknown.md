+++
author = "Daulton"
title = "GRUB: unknown filesystem on ZFS-based Proxmox"
date = "2021-07-11"
description = "Proxmox team has released a fix for this bug."
tags = [
    "guides",
]
categories = [
    "guides",
]
+++

Recently I experienced a very frustrating bug with one of the hosts in my Proxmox cluster. It was not the first time I experienced this bug, previously I was not aware of any fix and had resort to re-installing the system if this occurred. But now there is a fix.
<!--more-->

What would happen is you would update your Proxmox installation and a zpool upgrade on the 'rpool' occurs, then you reboot, and find that your system is unbootable when you see the following error:

```
Error: unknown filesystem.
grub rescue>
```

This would occur if the following points are true:

* System installed using Proxmox VE ISO 6.3 or earlier
* System uses GRUB legacy BIOS boot to boot
* System is using ZFS as the root filesystem

[Fortunately there is an official fix now!](https://pve.proxmox.com/wiki/ZFS:_Switch_Legacy-Boot_to_Proxmox_Boot_Tool)

#### Notes

* [Reference](https://forum.proxmox.com/threads/zfs-error-no-such-device-error-unknown-filesystem-entering-rescue-mode.75122/) 
