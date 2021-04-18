+++
author = "Daulton"
title = "Installing sabnzbd on FreeBSD"
date = "2020-04-22"
description = "Learn how to install Sabnzbd on FreeBSD, download usenet content from Sonarr, Radarr, etc!"
tags = [
    "freebsd",
    "guides",
]
categories = [
    "guides",
]
+++

SABnzbd is a cross-platform binary newsreader. It makes downloading from Usenet easy by automating the whole thing. You give it an NZB file or an RSS feed, it does the rest. Has a web-browser based UI and an API for 3rd-party apps. Ideal for servers too.

<!--more-->

If you are building your media setup and you are looking for a good binary newsreader to hook Sonarr, Radarr, etc. into I definitely recommend sabnzbd. It is a very versatile so you do a lot of nice things with it, for example with your various other services like Sonarr or Radarr you can have a category assigned such as 'Tv' and Sabnzbd can be configured to save the download to your preferred path. This way your different content can be saved to the correct location.

The end result is a [sabnzbd server](https://sabnzbd.org/ "https://sabnzbd.org/") running on FreeBSD.

----------

#### Installation

* Update first:

```
pkg update
pkg upgrade
```

* Install the Jackett package:

```
pkg install sabnzbd
```

* Start Sabnzbd at boot:

```
sysrc sabnzbd_enable=YES
```

* Next, start Sabnzbd with the following command:

```
service sabnzbd start
```

The IP address will depend upon the DHCP assigned address or whichever static address you give it if you did so. Verify the IP address with [ifconfig](https://www.freebsd.org/doc/en/articles/linux-users/network.html "https://www.freebsd.org/doc/en/articles/linux-users/network.html") or if this is a jail in FreeNAS you can view your jail IP address in the Jails tab under the IPv4 address column. Sabnzbd is now ready to use and you can navigate to it with this URL.

[http://x.x.x.x:8080/sabnzbd/](http://x.x.x.x:8080/sabnzbd/ "http://x.x.x.x:8080/sabnzbd/") or [http://localhost:8080/sabnzbd/](http://localhost:8080/sabnzbd/ "http://localhost:8080/sabnzbd/")

----------

#### Notes

* The config file is located is /usr/local/sabnzbd/sabnzbd.ini make sure you back it up.
