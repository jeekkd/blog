+++
author = "Daulton"
title = "Passing parameters to kernel with builtin command line"
date = "2018-08-28"
description = "How to pass parameters to the kernel with builtin command line"
tags = [
    "linux",
    "guides",
]
categories = [
    "guides",
]
+++

This guide is about using the Linux kernels builtin command line to pass parameters to use at boot. This can be set within your GRUB config of course, but for a movable kernel setting the options within the kernel itelf is best. This way your custom options will persist across computers, ideal if your kernel is on a flash drive.
<!--more-->

----------

1. Go to your kernel sources that you are configuring

```
cd /usr/src/linux
```

2. Open the menuconfig to configure the kernel

```
make menuconfig
```

3. Go in  **Processor type and features**  and enable the following option to 'Y': Built-in kernel command line

![config commandline](/images/kernel-boot-parmeter-guide/config-cmdline.png)

4. You can then select this and input your desired boot options here. Afterward save it and recompile, your options will be persistent

Example:

> snd-hda-intel.index=1,0

This example is necessary for some laptops that have both HDMI and an internal sound card. The default is often HDMI so to have the internal soundcard the default even though this is generally available through sound options in a desktop environment playback through web browsers for some reason requires the mapping be done a different way. This is mainly only relevant to Gentoo systems using only ALSA without PulseAudio.

Or

> net.ifnames=0

You could add the above, which is one of the methods to returning to the 'old' network interface naming scheme instead of the new one.
