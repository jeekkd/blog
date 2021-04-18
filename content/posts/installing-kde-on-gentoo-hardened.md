+++
author = "Daulton"
title = "Installing KDE on Gentoo hardened"
date = "2018-08-28"
description = "Installing KDE on Gentoo hardened"
tags = [
    "linux",
    "guides",
]
categories = [
    "guides",
]
+++

Installing [KDE](https://wiki.gentoo.org/wiki/KDE "https://wiki.gentoo.org/wiki/KDE") on [hardened](https://wiki.gentoo.org/wiki/Hardened_Gentoo "https://wiki.gentoo.org/wiki/Hardened_Gentoo")  requires additional USE flags set to get the necessary components enabled, so the desktop is complete.
<!--more-->

I have not encountered any issues running KDE under a hardened Gentoo, all common functionality that I have used works as intended.

----------

This begins with assuming you already are on a hardened profile. I am on hardened/linux/amd64 which according to the  [portage profile structure](https://wiki.gentoo.org/wiki/Profile_(Portage)#Structure "https://wiki.gentoo.org/wiki/Profile_(Portage)#Structure")  means my profile is located at /usr/portage/profiles/hardened/linux/amd64. Reference the given link to locate where your profile is located if you are on a different profile.

* Add the USE flags from:

> /usr/portage/profiles/targets/desktop/kde/make.defaults 

* to the global USE flags that are in: 

> /etc/portage/make.conf
    

This currently contains the following:

> USE="consolekit declarative dri kde kipi phonon plasma policykit semantic-desktop xcomposite xinerama xscreensaver"

* Append the contents of /usr/portage/profiles/targets/desktop/kde/package.use to your /etc/portage/package.use file
    

This currently contains the following:

```
# Resolve conflicts with slot 5 ebuilds
kde-frameworks/baloo minimal
kde-apps/kde4-l10n minimal

# Required by kde-frameworks/kauth
sys-auth/polkit-qt qt5

# Required by kde-frameworks/knotifications[dbus]
dev-libs/libdbusmenu-qt qt5

# Required by kde-frameworks/knotifyconfig[phonon]
media-libs/phonon qt5

# Required by media-libs/phonon[vlc]
media-libs/phonon-vlc qt5

# Required by kde-apps/kdenlive:5
media-libs/mlt kdenlive qt5 melt -kde -qt4

# Required by dev-qt/qtcore:5
dev-libs/libpcre pcre16

# Required by kde-frameworks/kcoreaddons
dev-qt/qtcore icu

# Required by kde-apps/kate[addons]
dev-libs/libgit2 threads

# Required by kde-apps/pykde4
dev-python/PyQt4 script sql webkit

# Required by kde-apps/akonadi
dev-qt/qtsql mysql

# Required by media-gfx/graphviz which is required by kde-apps/kcachegrind
media-libs/gd fontconfig

# Required by dev-db/virtuoso-server
sys-libs/zlib minizip

# Not required, but makes life easier with Qt; bug 457934
app-arch/unzip natspec

# Required by kde-apps/libkexiv2
media-gfx/exiv2 xmp

# Required by kde-apps/artikulate
dev-qt/qt-mobility multimedia

# Required by app-office/libreoffice
media-libs/phonon designer
```

----------

* Emerge KDE
    
```
emerge --ask kde-plasma/plasma-meta
```

Alternatively, kde-plasma/plasma-desktop provides the basic desktop, leaving users free to install only the extra packages they require

```
emerge --ask kde-plasma/plasma-desktop
```

* Upgrade all installed packages, dependencies, and deep dependencies that are outdated or have USE flag changes (avoiding unnecessary rebuilds when USE changes have no impact):
    
```
emerge -auDU --with-bdeps=y @world
```

----------

You will want to reference the  [KDE page on the Gentoo wiki](https://wiki.gentoo.org/wiki/KDE "https://wiki.gentoo.org/wiki/KDE")  to install any application bundles or widgets. Additionally if you want to use SDDM as your display manager as it integrates well with KDE,  [refer to the following](https://wiki.gentoo.org/wiki/SDDM "https://wiki.gentoo.org/wiki/SDDM").

