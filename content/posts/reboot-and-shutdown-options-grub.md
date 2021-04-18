+++
author = "Daulton"
title = "How-to add reboot and shutdown to grub"
date = "2018-08-28"
description = "How-to add reboot and shutdown to grub"
tags = [
    "linux",
    "guides",
]
categories = [
    "guides",
]
+++

To add Shutdown and Reboot options to your GRUB menu, you can do the following. This is nice do you don't need to Ctrl + Alt + Del to reboot, or press your power button shutdown!
<!--more-->

----------

* Edit /etc/grub.d/40_custom
    
```
vi /etc/grub.d/40_custom
```

* Add the below options
    
```
 menuentry "Reboot" {
	  reboot
}

menuentry "Shut Down" {
	  halt
}
```

* Update your grub.cfg
    
Gentoo:

```
grub-mkconfig -o /boot/grub/grub.cfg
```

Debian and derivatives:

```
update-grub
```
