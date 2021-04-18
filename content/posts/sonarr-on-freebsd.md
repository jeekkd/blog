+++
author = "Daulton"
title = "Installing sonarr on FreeBSD"
date = "2018-12-29"
description = "Learn how to install sonarr on FreeBSD to organize, automatically search, download, rename, etc your favourite TV shows."
tags = [
    "freebsd",
    "guides",
]
categories = [
    "guides",
]
+++

This guide also will also apply to [FreeNAS](http://www.freenas.org/ "http://www.freenas.org/") [FreeNAS 9.x jails.](http://doc.freenas.org/9.10/jails.html "http://doc.freenas.org/9.10/jails.html")  The end result is a  [Sonarr Server](https://www.plex.tv/ "https://github.com/sonarr/sonarr") running on FreeBSD or in a jail, either one you will end up with an awesome way to provide API Support for your favorite torrent trackers to use with Sonarr, sonarr, Headphones, etc.
<!--more-->

----------

#### Installation

1. Update first:

```
pkg update
pkg upgrade
```

2. Install the sonarr package:

```
pkg install sonarr
```

3. Start sonarr at boot:

```
sysrc sonarr_enable=YES
```

4. Next, start sonarr with the following command:

```
service sonarr start
```

The IP address will depend upon the DHCP assigned address or whichever static address you have assigned it if you did so. Verify the IP address with  [ifconfig](https://www.freebsd.org/doc/en/articles/linux-users/network.html "https://www.freebsd.org/doc/en/articles/linux-users/network.html") or if this is a jail in FreeNAS you can view your jail IP address in the Jails tab under the IPv4 address column. sonarr is now ready to use and you can navigate to it with this  URL.

[http://x.x.x.x:8989](http://x.x.x.x:8989 "http://x.x.x.x:8989")

----------

If you have other jails or services which need to be able to access the downloaded files (such as Sonarr, Plex, etc) then I advise the following. Change the UID of the daemon user for other service(s) in the your jails to have the same UID as the media user (or another user you create), that way Sonarr can do what it needs to with the files it is owned by a UID and GID readable and writable by all required services. This is not necessary for sonarr, NZBHydra, or other similar services because they do not interact with media on the filesystem at all.

```
sysrc sonarr_user=media
sysrc sonarr_group=media
```
  
----------

#### Rebuilding / migrating jail

1. Create new jail.
 
2. Set up storage for jail. In my case I have a dataset called Media that I mount as /media in the sonarr jail.
    
3. Start shell in new jail.
    
4. Follow the above installation instructions
    
5. Go back in to old jail, stop sonarr ('service sonarr stop') and backup /usr/local/sonarr. In my case I used tar to create an archive in /media called sonarr-data.tar.
  
```  
cd /media
tar -C /usr/local/sonarrdata -zcf sonarr-data.tar.gz .
```

6. Go back in to new jail. Start sonarr 'service sonarr start' and then stop it 'service sonarr stop'. Extract your backup archive to /usr/local/sonarr.
    
```
tar -xf /media/sonarr-data.tar.gz -C /usr/local/sonarr
```

7. Start sonarr in new jail 'service sonarr start'.
    
8. Delete old jail when you test that the new jail works correctly.
 
