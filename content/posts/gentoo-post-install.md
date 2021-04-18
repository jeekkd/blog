+++
author = "Daulton"
title = "Gentoo Post-Installation Setup Guide "
date = "2018-08-28"
lastmod = "2020-10-19"
description = "Post install setup guide to turn your base install into something complete."
tags = [
    "linux",
    "guides",
]
categories = [
    "guides",
]
+++

Gentoo is quite an interesting beast to get configured exactly how you want, there are a lot of variables to take into account as everything after the base system is installed is up to you to figure out. You start from a terminal and end up wherever you wish to go, learning a bit about everything as you go along and making it the way you want it to be. It certainly is not for the the impatient, it takes try or two and a couple days even to get it from a stage3 to a completed system.
<!--more-->

The following are important links, tips and examples I have came across that helped solve issue(s) so keeping track of them is valuable for future installations and anyone else who may be wrestling with Gentoo.

You may be interested in some of my scripts, many are made with Gentoo in mind so it may be of value to you to check out. You can find them [here in my Shell Scripts section](https://daulton.ca/2018/08/shell-scripts/) or [on my GitHub.](https://github.com/jeekkd "https://github.com/jeekkd")

----------

### Starting point

The [Gentoo](https://wiki.gentoo.org/wiki/Handbook:AMD64 "https://wiki.gentoo.org/wiki/Handbook:AMD64") [handbook](https://wiki.gentoo.org/wiki/Handbook:AMD64 "https://wiki.gentoo.org/wiki/Handbook:AMD64") gets a very necessary mention, it should be followed to the end to get the basic system up and running. The end result is a system with a terminal prompt, a bootloader, kernel, and networking support - essentially a very bare bones system.

Make sure to take your time reading every step as missing something could cause issues later. Make sure to spend extra time on configuring the kernel because if it does not work on your hardware it will cause issues later booting it. When you are going through the handbook as well as all the links given on this page, if you want to understand and use Gentoo effectively it is recommended to take your time reading and take things in. Otherwise you are copy pasting commands and when something goes wrong you may not understand what went wrong or how to fix it.

----------

### make.conf reference

It may be of value to reference a more complete make.conf to get some ideas for your own. In my [Notes section](https://daulton.ca/categories/notes/) I have [one such thing here](https://daulton.ca/2018/08/gentoo-make-conf/)

----------

### Background Information

If you have followed and successfully the [handbook](https://wiki.gentoo.org/wiki/Handbook:Main_Page "https://wiki.gentoo.org/wiki/Handbook:Main_Page")  you will be at a point in which your installation is comprised of the following components

* [Boot loader](http://www.linux.org/threads/linux-bootloaders.4489/ "http://www.linux.org/threads/linux-bootloaders.4489/")
* [Message bus system](https://wiki.archlinux.org/index.php/D-Bus "https://wiki.archlinux.org/index.php/D-Bus")  
* [Device manager](https://en.wikipedia.org/wiki/Udev "https://en.wikipedia.org/wiki/Udev") 
* [Init system](https://en.wikipedia.org/wiki/Systemd "https://en.wikipedia.org/wiki/Systemd")  
* [The Shell](https://www.gnu.org/software/bash/ "https://www.gnu.org/software/bash/")  
* [Shell Utilities](https://en.wikipedia.org/wiki/GNU_Core_Utilities "https://en.wikipedia.org/wiki/GNU_Core_Utilities")  
* [Linux Kernel](https://en.wikipedia.org/wiki/Wayland_(display_server) "https://en.wikipedia.org/wiki/Wayland_(display_server)")
* [Package](https://www.linode.com/docs/tools-reference/linux-package-management/ "https://www.linode.com/docs/tools-reference/linux-package-management/") [Manager](https://www.linode.com/docs/tools-reference/linux-package-management/ "https://www.linode.com/docs/tools-reference/linux-package-management/")
* [Daemons](http://www.sorgonet.com/linux/linuxdaemons/ "http://www.sorgonet.com/linux/linuxdaemons/") 
* [C Standard Library](https://en.wikipedia.org/wiki/C_standard_library "https://en.wikipedia.org/wiki/C_standard_library")
* Etc..
    
To have a usable system for our purposes, a graphical desktop system, we need the following additional components

* [X.org Graphical Server](https://wiki.gentoo.org/wiki/Xorg/Guide "https://wiki.gentoo.org/wiki/Xorg/Guide")  or  [Wayland](https://en.wikipedia.org/wiki/Wayland_(display_server) "https://en.wikipedia.org/wiki/Wayland_(display_server)")
* [Desktop Environment](http://www.howtogeek.com/163154/linux-users-have-a-choice-8-linux-desktop-environments/?PageSpeed=noscript "http://www.howtogeek.com/163154/linux-users-have-a-choice-8-linux-desktop-environments/?PageSpeed=noscript")
* [Display Manager](https://wiki.debian.org/DisplayManager "https://wiki.debian.org/DisplayManager")
* [Programs](https://wiki.archlinux.org/index.php/List_of_Applications "https://wiki.archlinux.org/index.php/List_of_Applications")
* [Wireless networking](https://www.linux.com/blog/linux-wireless-networking-short-walk "https://www.linux.com/blog/linux-wireless-networking-short-walk") 
* [Audio capabilities](https://wiki.gentoo.org/wiki/ALSA "https://wiki.gentoo.org/wiki/ALSA")
    
The above components will add onto the base later with each set of components. These different sets of components are interchangeable and able to be customized to an extent, allowing you choose which Desktop Environment you install - you could install KDE if you wanted.

----------

### Compiling the kernel

In the [kernel configuration section](https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/Kernel "https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/Kernel")  of the handbook it is mentioned that genkernel is an option to get going with a large and inclusive kernel. It doesn't mention that [sys-kernel/genkernel-next](https://packages.gentoo.org/packages/sys-kernel/genkernel-next "https://packages.gentoo.org/packages/sys-kernel/genkernel-next") is available that fixes some bugs with genkernel. As an example the following would work on genkernel-next but not genkernel for me for whatever reason.

```
genkernel --menuconfig --install all
```

If you are making your kernel .config from scratch [a tool called kergen can help](https://packages.gentoo.org/packages/sys-kernel/kergen "https://packages.gentoo.org/packages/sys-kernel/kergen") with the hardware detection and make appropriate selections for you. Or if you are not concerned with being overly minimal, you can give [gentoo-kernel-bin](https://packages.gentoo.org/packages/sys-kernel/gentoo-kernel-bin) a go.

You may be interested in my [build_gentoo_kernel](https://github.com/jeekkd/gentoo-kernel-build "https://github.com/jeekkd/gentoo-kernel-build") script which is quite comprehensive - check it out it may help you!

**Note:** _If you select all your own kernel options rather then using something like genkernel to get a configuration started, I would recommend going through the Gentoo Wiki for the various components you will want on your system. Want I mean is that as an example iptables requires certain kernel options set to work, so going through and adding those while you are making your kernel or doing so after you have verified your system functions is a good idea._

----------

### Installing Xorg

[Getting Xorg is the next step](https://wiki.gentoo.org/wiki/Xorg/Guide "https://wiki.gentoo.org/wiki/Xorg/Guide")  as you need X if you want a graphical environment of any sort, so this is the first step in getting your system a graphical display.

* Have the correct kernel options enabled, make sure to read the wiki carefully  
* Modify your /etc/portage/make.conf with the input and video devices you have. Example:

```
# For mouse, keyboard, and Synaptics touchpad support
INPUT_DEVICES="evdev synaptics keyboard mouse"
# Video devices
VIDEO_CARDS="nouveau intel"
```

[Remember to reference the following if your video card is not nvidia and your cpu is not intel.](https://wiki.gentoo.org/wiki//etc/portage/make.conf#VIDEO_CARDS "https://wiki.gentoo.org/wiki//etc/portage/make.conf#VIDEO_CARDS")

* Emerge the X server and the necessary drivers

```
emerge --ask -q x11-base/xorg-drivers && env-update && source /etc/profile
```

To test X we need the following

```
emerge --ask -q x11-wm/twm x11-terms/xterm
```

When you've installed Xorg and the mentioned packages you can boot into the system and then test it by typing the following into the terminal after logging in.

```
startx
```

**Note:** _I would suggest following the guide and assuring to install  [x11-wm/twm](https://packages.gentoo.org/packages/x11-wm/twm "https://packages.gentoo.org/packages/x11-wm/twm")  and  [x11-terms/xterm](https://packages.gentoo.org/packages/x11-terms/xterm "https://packages.gentoo.org/packages/x11-terms/xterm")  to test if Xorg was successfully installed and working properly. This is important because if you do not test it before moving on it causes a lot of headache to figure out exactly what the issue is as it could be Xorg, the display manager, desktop environment, graphic drivers, etc causing display oriented issues if you just install everything at once and go on without testing first._

----------

### Installing a Desktop Environment

Next would be getting a [desktop environment](https://wiki.gentoo.org/wiki/Xfce/Guide "https://wiki.gentoo.org/wiki/Xfce/Guide") or window manager of some sort installed. Personally I quite like  [XFCE](https://wiki.gentoo.org/wiki/Xfce "https://wiki.gentoo.org/wiki/Xfce")  as it is minimal but yet has all the features you would normally need. There are others you can install so take a look at other  [desktop environments](https://wiki.gentoo.org/wiki/Category:Desktop_environment "https://wiki.gentoo.org/wiki/Category:Desktop_environment")  or  [window managers.](https://wiki.gentoo.org/wiki/Category:Window_manager "https://wiki.gentoo.org/wiki/Category:Window_manager")

Another top quality choice [worth considering is KDE](https://wiki.gentoo.org/wiki/KDE "https://wiki.gentoo.org/wiki/KDE"). While heavier in terms of resource usage compared to XFCE, it is a very beautiful, featureful, stable and highly customizable choice.

Your Desktop Environment choice impacts your Display Manager decision as well. If you choose KDE as example then you can skip choosing a Display Manager as  [it comes with its own called SDDM](https://wiki.gentoo.org/wiki/SDDM "https://wiki.gentoo.org/wiki/SDDM"). It is recommended to go with SDDM if you use KDE because they have integrated and blended it in nicely with the rest of the system, as another Display Manager may look out of place if you sort of thing is of concern.

----------

### Installing a Display Manager

While not completely necessary it does make things easier, a display manager will provide a graphical login manager that will start your graphical environment along with any supplementary things you may want. For me,  [LightDM](https://wiki.gentoo.org/wiki/LightDM "https://wiki.gentoo.org/wiki/LightDM")  has been great it 'just works' as it requires no set up or tinkering. There are others available  [so take a look](https://wiki.gentoo.org/wiki/Display_manager "https://wiki.gentoo.org/wiki/Display_manager") if you want a more beautiful one, or one that has more features.

A good and easy option is LightDM. To use it simply emerge it

```
emerge --ask -q x11-misc/lightdm
```

And then add lightdm to /etc/conf.d/xdm

> DISPLAYMANAGER="lightdm"

**Regardless of which display manager you use the following is required**  so whichever display manager you use is launched during the boot process by the OpenRC init system.

```
rc-update add xdm default
```

----------

### Installing NetworkManager

**Note:** _This is an optional, ease of use component. It is not required but it definitely does make things easier._

I like [Network Manager](https://wiki.gentoo.org/wiki/NetworkManager "https://wiki.gentoo.org/wiki/NetworkManager"), it makes network management pretty easy I do not need to fiddle with configuration files or anything to get my networking working. So I like to use Network Manager with  [nm-applet](https://wiki.gentoo.org/wiki/NetworkManager#NetworkManager_GUI_bits_in_GTK "https://wiki.gentoo.org/wiki/NetworkManager#NetworkManager_GUI_bits_in_GTK")  to get a point and click set up, this is great for me because  [I also use a VPN](https://wiki.gentoo.org/wiki/NetworkManager#NetworkManager_VPN_plugins "https://wiki.gentoo.org/wiki/NetworkManager#NetworkManager_VPN_plugins")  so to be able to connect/disconnect and configure it easily is wonderful.

**Assure that you are in the plugdev group**  so you can manage network interfaces

```
gpasswd -a <user_name> plugdev
```

NetworkManager should also start at boot time at the default run level

```
rc-update add NetworkManager default
```

----------

### Configuring modules to load at boot

This step is optional in the sense that if you do not have any extra modules you want to load at boot, then you do not need to do this. As an example for my USB to serial adapter instead of probing the module every time I use it, I can have it automatically load.

Assure that you have '_modules_' at the boot level by doing the following command

```
rc-update show
```

If you do not have 'modules' starting at the boot runlevel then do the following:

```
rc-update add modules boot
```

To add a module to  _/etc/conf.d/modules_  is pretty straight forward, within the quotes add the name of the module you want to start at boot at then space delimit them:

> modules="cp210x"

----------

### Configuring Wireless

Wireless can be a bit tricky, but  [with the following guide](https://wiki.gentoo.org/wiki/Wifi "https://wiki.gentoo.org/wiki/Wifi")  and assuring you have installed  [sys-kernel/linux-firmware](https://packages.gentoo.org/packages/sys-kernel/linux-firmware "https://packages.gentoo.org/packages/sys-kernel/linux-firmware")  the configuration is not too bad. If you are using wireless I would recommend installing NetworkManager with nm-appet as well, this way you have a easy graphical way to manage your wireless.

**Note:** _If you are not using NetworkManager, make sure you  [have done the following](https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/System#Automatically_start_networking_at_boot "https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/System#Automatically_start_networking_at_boot") but with your wireless interface so it can be started at boot_

----------

### Installing a bootloader

You will need a bootloader unless you figure a EFI stub kernel,  [here are some options](https://wiki.gentoo.org/wiki/Bootloader "https://wiki.gentoo.org/wiki/Bootloader").  [Lilo](https://wiki.gentoo.org/wiki/LILO "https://wiki.gentoo.org/wiki/LILO")  is the easiest I have found for standard configurations as you can see below it is quite simple looking, but there is nothing wrong with using GRUB2 and its automatic configuration generation either. The Gentoo Wiki has an excellent section on  [configuring the bootloader](https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/Bootloader "https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/Bootloader").

The example configuration file has a lot of examples and comments so read them and you will catch on. The following is an example of how simple LILO configurations are. Edit /etc/lilo.conf

```
boot=/dev/sda
prompt timeout=50
default=gentoo
lba32

image=/boot/kernel-3.11.2-gentoo
  label=Gentoo
  read-only
  root=/dev/sda4
  append="quiet"
```

**Installing LILO on the MBR**

Then in order to install LILO on the MBR, invoke the lilo command. However, before doing that, the /etc/lilo.conf file must be set up of course

```
root# lilo
```

----------

### Audio with ALSA

You'll likely want to have audio, so take a look at [ALSA](https://wiki.gentoo.org/wiki/ALSA "https://wiki.gentoo.org/wiki/ALSA"). Pulseaudio in most cases is unnecessary and adds more layers of abstraction, this can make things a pain to debug if something is wrong or doesn't work.

If you are using xfce consider getting the [xfce-extra/xfce4-mixer](https://packages.gentoo.org/packages/xfce-extra/xfce4-mixer "https://packages.gentoo.org/packages/xfce-extra/xfce4-mixer")panel applet so you can control the volume from the panel bar as you normally would. Additionally for xfce there's  [xfce-extra/xfce4-volumed](https://packages.gentoo.org/packages/xfce-extra/xfce4-volumed "https://packages.gentoo.org/packages/xfce-extra/xfce4-volumed")  which is a daemon to control volume up/down and mute keys for ALSA.

----------

### Maintaining classic interface naming

One may want to keep the classic interface naming scheme for their network interfaces so  [here is how you do so](https://wiki.gentoo.org/wiki/Eudev#Keep_classic_.27eth0.27_naming "https://wiki.gentoo.org/wiki/Eudev#Keep_classic_.27eth0.27_naming"). The easiest way I found and is how I do it, is to add net.ifnames=0 to the kernel boot options which for me and using  [Lilo](https://wiki.gentoo.org/wiki/Lilo "https://wiki.gentoo.org/wiki/Lilo")  looks like so:

```
boot=/dev/sda
prompt
timeout=50
default=gentoo
lba32

image=/boot/vmlinuz-4.3.3
label=gentoo
read-only  # Start with a read-only root. Do not alter!
**append="root=/dev/sda1 net.ifnames=0"**
initrd=/boot/initramfs-genkernel-x86_64-4.3.3
```

If you are using GRUB it will be a little different, [take a look here](https://wiki.gentoo.org/wiki/GRUB2#Setting_configuration_parameters "https://wiki.gentoo.org/wiki/GRUB2#Setting_configuration_parameters") and the variable in question is "GRUB_CMDLINE_LINUX_"_which you can add "net.ifnames=0" to.

In your /etc/default/grub it would look like so:

> GRUB_CMDLINE_LINUX="net.ifnames=0"

After that install GRUB to your disk and make an updated configuration file:

```
grub-install /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg
```

----------

### Installing iptables

A firewall is necessary and as one may already know [iptables is a very powerful way](https://wiki.gentoo.org/wiki/Iptables "https://wiki.gentoo.org/wiki/Iptables")  to filter traffic. The guide  [gives a starting point with a small script](https://wiki.gentoo.org/wiki/Iptables#Generating_firewall_rules "https://wiki.gentoo.org/wiki/Iptables#Generating_firewall_rules")  to get you up and running, from there it is recommended to read up on adding your own rules if you wish to allow or block anything else.

For users wishing very restrict filewalls and are willing to put some time into tinkering with it to get it down to a least access sort of level where its granular to the point only specific ports are allowed in and out, only some can come back in though established sessions, etc. [should take a look at my iptables script](https://gitlab.com/huuteml/restricted_iptables "https://gitlab.com/huuteml/restricted_iptables")

Alternatively you can use something [such as ufw](https://wiki.gentoo.org/wiki/Ufw "https://wiki.gentoo.org/wiki/Ufw") and then a front-end for it such as  [ufw-frontends](https://packages.gentoo.org/packages/net-firewall/ufw-frontends "https://packages.gentoo.org/packages/net-firewall/ufw-frontends"). This provides an easy graphical way to manage your firewall.

----------

### Install some programs

By this point one should have a fully functional system but it is lacking something still! You need applications,  [so here is a list of recommended applications](https://wiki.gentoo.org/wiki/Recommended_applications "https://wiki.gentoo.org/wiki/Recommended_applications")  to get you started.

Additionally  [here is a cheat sheet](https://wiki.gentoo.org/wiki/Gentoo_Cheat_Sheet "https://wiki.gentoo.org/wiki/Gentoo_Cheat_Sheet") as well to give you some tips on installing, removing, updating, etc packages on your system.

----------

### Make your fonts nice

Consider making your fonts nicer, at least on XFCE I did notice it could probably be a little nicer. A very good reference for this  [is in the Gentoo Wiki, in a guide simply called Fontconfig](https://wiki.gentoo.org/wiki/Fontconfig "https://wiki.gentoo.org/wiki/Fontconfig")

----------

### Fixes

* If your wallpaper does not show up if you're using xfce4 you may need to do the following, this worked for me when I had this error and the wallpaper would not change otherwise. First try this:

Kill the xfdesktop process and then do the following as root in a terminal

```
xfdesktop &
```

If the above does not work try the following, do note this will reset your xfce environment to stock so your modifications will be reset. Create temporary backups by moving each folder to a .bak extension and then logging out and then back in to regenerate the environment.

```
rm -r ~/.cache/sessions
rm -r ~/.config/xfce*
rm -r ~/.config/Thunar
```

* Assure that as you go you are checking your kernel configuration, as an example iptables requires additional kernel options enabled so for it to work you must set those as required. This means you'll be recompiling often through out the process, especially as you don't want to do too much at one time since that will cause much more work debugging which option is causing trouble.

----------

### Extra reading

* It is recommended to read through the [Working with Gentoo](https://wiki.gentoo.org/wiki/Handbook:AMD64/Working/Portage "https://wiki.gentoo.org/wiki/Handbook:AMD64/Working/Portage")and Working with Portage sections of the  [Gentoo handbook](http://wiki.gentoo.org/wiki/Handbook:AMD64 "http://wiki.gentoo.org/wiki/Handbook:AMD64")  so you may understand the system in more depth. Between portage, emerge, USE flags, and knowing where and what certain files are there is a lot of power there and a lot to know. So knowing how to utilize these things is a great benefit if you wish to have knowledge of your system and have fine grained tuning, customization, and control.
* For using your system it will come in handy knowing how to manage it,  [here is a useful cheat sheet](https://wiki.gentoo.org/wiki/Gentoo_Cheat_Sheet "https://wiki.gentoo.org/wiki/Gentoo_Cheat_Sheet")  that has various Gentoo oriented commands with examples for updating, installation and removal of packages, USE flags, etc.
* Consider following [Sakaki's EFI guide](https://wiki.gentoo.org/wiki/Sakaki's_EFI_Install_Guide "https://wiki.gentoo.org/wiki/Sakaki's_EFI_Install_Guide")  if the end product is something desirable, he goes through a lot of great learning and theory as you go so it is very educational. The end product is quite nice too
