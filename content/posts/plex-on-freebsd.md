+++
author = "Daulton"
title = "Installing Plex Media Server on FreeBSD"
date = "2018-08-28"
description = "This guide will help you install Plex Media Server on FreeBSD."
tags = [
    "freebsd",
    "guides",
]
categories = [
    "guides",
]
+++

This guide also will also apply to [FreeNAS](http://www.freenas.org/ "http://www.freenas.org/") [FreeNAS 9.x jails.](http://doc.freenas.org/9.10/jails.html "http://doc.freenas.org/9.10/jails.html")  The end result is a  [Plex Media Server](https://www.plex.tv/ "https://www.plex.tv/")  running on FreeBSD or in a jail, either one you will end up with a stable and reliable media server.
<!--more-->

----------

#### Installation

1. Update first:

```
pkg update
pkg upgrade
```

2. Install the Plex package:

```
pkg install plexmediaserver
```

3. Start Plex at boot:

```
sysrc plexmediaserver_enable=YES
```

4. Next, start Plex with the following command:

```
service plexmediaserver start
```

The IP address will depend upon the DHCP assigned address, or whichever static address you have assigned it. Verify the IP address with  [ifconfig](https://www.freebsd.org/doc/en/articles/linux-users/network.html "https://www.freebsd.org/doc/en/articles/linux-users/network.html"). Plex is now ready to use and you can navigate to it with this  URL.

[http://x.x.x.x:32400/web](http://x.x.x.x:32400/web "http://x.x.x.x:32400/web")

If you would like to change to the latest repo to get faster updates to Plex than every month or so you can do that by adding the latest package repo.

```
mkdir -p /usr/local/etc/pkg/repos
vi /usr/local/etc/pkg/repos/FreeBSD.conf
```

Then add the following contents to FreeBSD.conf:

```
FreeBSD: {
	url: "pkg+http://pkg.FreeBSD.org/${ABI}/latest"
}
```

----------

#### Rebuilding / migrating jail

1. Create new jail.
 
2. Set up storage for jail. In my case I have a dataset called Media that I mount as /media in the plex jail.
    
3. Start shell in new jail.
    
4. Follow the above installation instructions
    
5. Go back in to old jail, stop plex ('service plexmediaserver stop') and backup /usr/local/plexdata. In my case I used tar to create an archive in /media called plexdata.tar.

``` 
cd /media
tar -C /usr/local/plexdata -zcf plexdata.tar.gz .
```

6. Go back in to new jail. Start plexmediaserver 'service plexmediaserver start' and then stop it 'service plexmediaserver stop'. Extract your backup archive to /usr/local/plexdata.
    
```
tar -xf /media/plexdata.tar.gz -C /usr/local/plexdata
```

7. Start plex in new jail 'service plexmediaserver start'.
    
8. Delete old jail when you test that the new jail works correctly.


