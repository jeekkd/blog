+++
author = "Daulton"
title = "Installing Jackett on FreeBSD"
date = "2018-12-26"
description = "Learn how to install Jackett on FreeBSD, get API Support for your favorite torrent trackers to use with jackett, Radarr, Headphones, etc!"
tags = [
    "freebsd",
    "guides",
]
categories = [
    "guides",
]
+++

This guide also will also apply to [FreeNAS](http://www.freenas.org/ "http://www.freenas.org/")  [FreeNAS 9.x jails.](http://doc.freenas.org/9.10/jails.html "http://doc.freenas.org/9.10/jails.html")  The end result is a  [Jackett Server](https://www.plex.tv/ "https://github.com/Jackett/Jackett") running on FreeBSD or in a jail, either one you will end up with an awesome way to provide API Support for your favorite torrent trackers to use with jackett, Radarr, Headphones, etc.
<!--more-->

----------

#### Installation

* Update first:

```
pkg update
pkg upgrade
```

* Install the Jackett package:

```
pkg install jackett
```

* Start Jackett at boot:

```
sysrc jackett_enable=YES
```

* Next, start Jackett with the following command:

```
service jackett start
```

The IP address will depend upon the DHCP assigned address or whichever static address you give it if you did so. Verify the IP address with  [ifconfig](https://www.freebsd.org/doc/en/articles/linux-users/network.html "https://www.freebsd.org/doc/en/articles/linux-users/network.html") or if this is a jail in FreeNAS you can view your jail IP address in the Jails tab under the IPv4 address column. Jackett is now ready to use and you can navigate to it with this  URL.

[http://x.x.x.x:9117](http://x.x.x.x:9117 "http://x.x.x.x:9117")

----------

#### Rebuilding / migrating jail

1. Create new jail.
 
2. Set up storage for jail. In my case I have a dataset called Media that I mount as /media in the jackett jail.
    
3. Start shell in new jail.
    
4. Follow the above installation instructions
    
5. Go back in to old jail, stop jackett ('service jackett stop') and backup /usr/local/jackett. In my case I used tar to create an archive in /media called jackett-data.tar.
   
```    
cd /media
tar -C /usr/local/jackettdata -zcf jackett-data.tar.gz .
```

6. Go back in to new jail. Start jackett 'service jackett start' and then stop it 'service jackett stop'. Extract your backup archive to /usr/local/jackett.
    
```
tar -xf /media/jackett-data.tar.gz -C /usr/local/jackett
```
 
7. Start jackett in new jail 'service jackett start'.
    
8. Delete old jail when you test that the new jail works correctly.
 
