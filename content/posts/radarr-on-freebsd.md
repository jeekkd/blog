+++
author = "Daulton"
title = "Installing radarr on FreeBSD"
date = "2018-12-29"
description = "Learn how to install radarr on FreeBSD to organize, automatically search, download, rename, etc your favourite movies."
tags = [
    "freebsd",
    "guides",
]
categories = [
    "guides",
]
+++

This guide also will also apply to [FreeNAS](http://www.freenas.org/ "http://www.freenas.org/") [FreeNAS 9.x jails.](http://doc.freenas.org/9.10/jails.html "http://doc.freenas.org/9.10/jails.html")  The end result is a  [radarr Server](https://www.radarr.tv/ "https://github.com/radarr/radarr") running on FreeBSD or in a jail, either one you will end up with an awesome way to provide API Support for your favorite torrent trackers to use with radarr, Radarr, Headphones, etc.
<!--more-->

----------

#### Installation

1. Update first:

```
pkg update
pkg upgrade
```

2. Install the radarr package:

```
pkg install radarr
```

3. Start radarr at boot:

```
sysrc radarr_enable=YES
```

4. Next, start radarr with the following command:

```
service radarr start
```

The IP address will depend upon the DHCP assigned address or whichever static address you give it if you did so. Verify the IP address with  [ifconfig](https://www.freebsd.org/doc/en/articles/linux-users/network.html "https://www.freebsd.org/doc/en/articles/linux-users/network.html") or if this is a jail in FreeNAS you can view your jail IP address in the Jails tab under the IPv4 address column. radarr is now ready to use and you can navigate to it with this  URL.

[http://x.x.x.x:7878](http://x.x.x.x:7878 "http://x.x.x.x:7878")

----------

If you have other jails or services which need to be able to access the downloaded files (such as radarr, radarr, etc) then I advise the following. Change the UID of the daemon user for other service(s) in the your jails to have the same UID as the media user (or another user you create), that way radarr can do what it needs to with the files it is owned by a UID and GID readable and writable by all required services. This is not necessary for radarr, NZBHydra, or other similar services because they do not interact with media on the filesystem at all.

```
sysrc radarr_user=media
sysrc radarr_group=media
```

----------

#### Rebuilding / migrating jail

1. Create new jail.
 
2. Set up storage for jail. In my case I have a dataset called Media that I mount as /media in the radarr jail.
    
3. Start shell in new jail.
    
4. Follow the above installation instructions
    
5. Go back in to old jail, stop radarr ('service radarr stop') and backup /usr/local/radarr. In my case I used tar to create an archive in /media called radarr-data.tar.

```  
cd /media
tar -C /usr/local/radarrdata -zcf radarr-data.tar.gz .
```

6. Go back in to new jail. Start radarr 'service radarr start' and then stop it 'service radarr stop'. Extract your backup archive to /usr/local/radarr.
    
```
tar -xf /media/radarr-data.tar.gz -C /usr/local/radarr
```

7. Start radarr in new jail 'service radarr start'.
    
8. Delete old jail when you test that the new jail works correctly.
 
