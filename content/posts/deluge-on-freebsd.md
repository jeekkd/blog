+++
author = "Daulton"
title = "Installing headless deluged on FreeBSD"
date = "2018-08-28"
description = "The end result is a headless Deluge torrent server running on FreeBSD or in a jail."
tags = [
    "freebsd",
    "guides",
]
categories = [
    "guides",
]
+++

This guide also will also apply to  [FreeNAS](http://www.freenas.org/ "http://www.freenas.org/") Jails. The end result is a headless Deluge torrent server running on FreeBSD or in a jail. It will be able to be used via the built in web interface, or self-hosted services like Sonarr can tie into it by the API.
<!--more-->

----------

#### Installation

1. [Add storage to the Jail](https://www.ixsystems.com/documentation/freenas/11/jails.html#add-storage) if it is a FreeNAS jail and you want to download your media to specific places. Lets say for example your media collection is located at /mnt/pool0/media/ and you have a Downloads folder among others for Movies, etc, as well. You can mount that to the Jail under /media, then in the Deluge web UI later on you can set your downloads to go to /media/Downloads by going to Preferences > Downloads category > and then setting 'Download To' to be /media/Downloads.

2. Update first

```
pkg update
pkg upgrade 
```

3. Install Deluge

```
pkg install deluge-cli
```

4. Set required options and create /usr/local/deluge

```
mkdir -p /usr/local/deluge
chown -R media:media /usr/local/deluge
sysrc deluged_enable=YES
sysrc deluged_user=media
sysrc deluged_group=media
sysrc deluged_confdir=/usr/local/deluge
sysrc deluge_web_enable=YES
sysrc deluge_web_user=media
sysrc deluge_web_group=media
sysrc deluge_web_confdir=/usr/local/deluge
```

5. Next, start Deluge with the following command:

```
service deluged start
```

6. Assure Deluge is running:

```
service deluged status
```

-   If you have other jails or services which need to be able to access the downloaded files (such as Sonarr, Plex, etc) then I advise the following. Change the UID of the daemon user for other service(s) in the your jails to have the same UID as the media user (or another user you create), that way when deluge stores files it is owned by a UID and GID readable and writable by all required services. This is not necessary for Jackett, NZBHydra, or other similar services because they do not interact with media on the filesystem at all.
-   Some plugins to look into are Scheduler and Labels. The Labels plugin is very useful if you use Sonarr, Radarr, or Headphones.
-   The IP address will depend upon the DHCP assigned address or whichever static address you give it. Verify the IP address with  [ifconfig](https://www.freebsd.org/doc/en/articles/linux-users/network.html "https://www.freebsd.org/doc/en/articles/linux-users/network.html"). Deluge is now ready to use and you can navigate to it with this URL.
- 	To speed up seeding, I recommend looking to the [itconfig deluge egg plugin](https://github.com/ratanakvlun/deluge-ltconfig). Note you do not need to compile or build it, just download the .egg from the release page and install it via the web UI by going to Preferences > Plugins > Install then navigate to the file on your local PC.

[http://x.x.x.x:8112/](http://x.x.x.x:8112/ "http://x.x.x.x:8112/")

----------

#### Rebuilding / migrating jail

After large FreeNAS updates it becomes necessary, unfortunately, to rebuild jails to continue being able to update them. These are instructions of what is required to preserve configurations for the migration to make rebuilding the jail as painless as possible.

1. Create new jail.
    
2. Set up storage for jail. In my case I have a dataset called Media that I mount as /media in the Deluge jail.
    
3. Start shell in new jail.
    
4. Follow the above installation instructions.
    
5. Go back in to old jail, stop Deluge ('service deluged stop') and backup /usr/local/deluge. In my case I used tar to create an archive in /media called deluge-data.tar.
  
```  
cd /media
tar -C /usr/local/deluge -zcf deluge-data.tar.gz .
```

6. Go back in to new jail. Start Deluge 'service deluged start' and then stop it 'service deluged stop'. Extract your backup archive to /usr/local/deluge
    
```
tar -xf /media/deluge-data.tar.gz -C /usr/local/deluge
```

7. Start Deluge in new jail 'service deluged start'.
    
8.  Delete old jail when you test that the new jail works correctly.

