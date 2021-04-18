+++
author = "Daulton"
title = "Installing NZBHydra on FreeBSD"
date = "2020-05-14"
description = "Learn how to install NZBHydra on FreeBSD, search usenet content from Sonarr, Radarr, etc!"
tags = [
    "freebsd",
    "guides",
]
categories = [
    "guides",
]
+++

[NZBHydra](https://github.com/theotherp/nzbhydra2) is a meta search for NZB indexers. It provides easy access to a number of raw and newznab based indexers. You can search all your indexers from one place and use it as indexer source for tools like Sonarr or CouchPotato.

<!--more-->

This makes NZBHydra a huge convenience by being able to setup all of your indexers, prioritize them how you like, etc., and then set up a single indexer connection to NZBHydra from each of your various locations such as Sonarr, Radarr, etc. This is way its centralized instead of duplicating the effort of adding each indexer one by one into each location you intend to use them from.

----------

#### Installation

* Update first:

```
pkg update
pkg upgrade
```

* Install the NZBHydra package:

```
pkg install nzbhydra2
```

* Start NZBHydra at boot:

```
sysrc nzbhydra2_enable=YES
```

* Adjust folder permissions so NZBHydra updates work from the web UI

```
chown -R nzbhydra2:nzbhydra2 /usr/local/nzbhydra2
chown -R nzbhydra2:nzbhydra2 /usr/local/share/nzbhydra2
```

* Next, start NZBHydra with the following command:

```
service nzbhydra2 start
```

The IP address will depend upon the DHCP assigned address or whichever static address you give it if you did so. Verify the IP address with [ifconfig](https://www.freebsd.org/doc/en/articles/linux-users/network.html "https://www.freebsd.org/doc/en/articles/linux-users/network.html") or if this is a jail in FreeNAS you can view your jail IP address in the Jails tab under the IPv4 address column. NZBHydra is now ready to use and you can navigate to it with this URL.

[http://x.x.x.x:5076](http://x.x.x.x:5076 "http://x.x.x.x:5076") or [http://localhost:5076](http://localhost:5076 "http://localhost:5076")

----------

#### Notes

* Under the System tab, then Backup, there is the ability to take a backup and download it. After you are set I recommend doing so encase you ever have an issue or need to rebuild.
* Please be aware that on [freshports this package](https://www.freshports.org/news/nzbhydra2/) is listed has an expiration date of 2020-09-15, this is likely due to using Python 2.7. I am not sure what will happen after that if it will only be available in ports or what.
