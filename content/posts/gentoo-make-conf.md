+++
author = "Daulton"
title = "Gentoo make.conf example file "
date = "2018-08-27"
description = "Gentoo make.conf example file "
tags = [
    "linux",
]
categories = [
    "notes",
]
+++

This is my current [make.conf](https://wiki.gentoo.org/wiki//etc/portage/make.conf "https://wiki.gentoo.org/wiki//etc/portage/make.conf"), I am sharing it for the sake of reference.
<!--more-->

Please do not copy it directly, the global USE flags, VIDEO_CARDS, CPU_FLAGS_X86 likely would not suite your installation or supported CPU extensions. For those things as well as FEATURES you will want to customize it to your needs and liking.

```  
# Please consult /usr/share/portage/config/make.conf.example for a more detailed example.

CFLAGS="-O2 -pipe -march=native"
CXXFLAGS="${CFLAGS}"

WARNING: Changing your CHOST is not something that should be done lightly.
Please consult http://www.gentoo.org/doc/en/change-chost.xml before changing.
CHOST="x86_64-pc-linux-gnu"

# Use the 'stable' branch - 'testing' no longer required for Gnome 3.
ACCEPT_KEYWORDS="amd64"

# USE flags
# These are global, change accordingly to your desired configuration and read the wiki for your window manager or desktop 
# environment since specific use flags may be recommended such as how XFCE does.
# These are for a KDE system on a hardened profile.
USE="branding bindist ipv6 consolekit gpm mtp opengl X dbus -modemmanager jpeg \
lock session startup-notification -wireless udev -gnome -systemd -minimal alsa semantic-desktop phonon \
exif glamor qt3support mtp infinality pam tcpd ssl spell flac vorbis cups xinerama xscreensaver \
truetype infinality xcb udisks upower egl policykit png pdf mp3 x264 mng tiff xml ogg pngsdl \
-wifi -handbook -mysql lcms xcomposite libnotify qml kipi custom-cflags custom-optimization \
gtk gtk2 qt5 -qt4 kde -cups networkmanager"

# Video cards for X11 drivers
# https://wiki.gentoo.org/wiki/Template:VIDEO_CARDS
# Additionally check out the wiki page for your vendors video cards
VIDEO_CARDS="radeon radeonsi vesa fbdev"

# Input devices for Xorg
# If you have a touch pad add 'synaptics'
INPUT_DEVICES="evdev keyboard mouse"

# Make concurrency level
# -j should be total amount of cores/threads - 1 usually. So a system with 8 cores/threads might have 7 here
MAKEOPTS="-j7 -l8"

# Supported CPU flags to be used for USE
# Use app-portage/cpuid2cpuflags to find your CPUs supported flags and then put them here. Then recompile packages that have 
# support for those new CPU flags: emerge --ask --changed-use --deep @world
CPU_FLAGS_X86="mmx mmxext sse sse2 sse3 ssse3"

# Gentoo mirrors
# Change accordingly to nearby mirrors https://gentoo.org/downloads/mirrors/
GENTOO_MIRRORS="rsync://rsync.gtlib.gatech.edu/gentoo ftp://gentoo.netnitco.net/pub/mirrors/gentoo/source/ \
rsync://gentoo.cs.uni.edu/gentoo-distfiles"

# FEATURES defines actions portage takes by default. This is an incremental
# variable. See the make.conf(5) man page for a complete list of supported
# values and their respective meanings. 

# For the webrsync-gpg feature addtional configuration is required outside of the make.conf 
# https://wiki.gentoo.org/wiki/Sakaki%27s_EFI_Install_Guide/Using_Your_New_Gentoo_System#Switching_to_emerge-webrsync_for_Security_.28Optional.29

FEATURES="webrsync-gpg sandbox sign userpriv usersandbox sfperms userfetch strict parallel-fetch"

# If the ccache feature is enabled, leave this
# CCACHE_SIZE="2G"

# This will install the keyring to the /var/lib/gentoo/gkeys/keyrings/gentoo/release 
# location. 
PORTAGE_GPG_DIR="/var/lib/gentoo/gkeys/keyrings/gentoo/release"

# Disable 'emerge --sync' so emerge-webrsync has to be used
SYNC=""

# EMERGE_DEFAULT_OPTS allows emerge to act as if certain options are specified on every run.
# Useful options include --ask, --verbose, --usepkg and many others. Options that are not
# useful, such as --help, are not filtered. 
EMERGE_DEFAULT_OPTS="--quiet-build=y --keep-going --jobs=8 --load-average=8"

# languages for localization reference below link
# https://wiki.gentoo.org/wiki//etc/portage/make.conf#LINGUAS
LINGUAS="en en_US en_GB"
L10N="en en-US en-GB"
```

After making changes to INPUT_DEVICES, VIDEO_CARDS, USE, etc you should update the system using the following command so the changes take effect:

```
emerge --ask --changed-use --deep @world
```

