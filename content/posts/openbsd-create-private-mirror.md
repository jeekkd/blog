+++
author = "Daulton"
title = "OpenBSD: Create a private amd64 mirror"
date = "2018-10-17"
description = "The following will walk through how to create a private amd64 mirror on OpenBSD. We will use httpd and do the mirror syncing via rsync."
tags = [
    "openbsd",
    "guides",
]
categories = [
    "guides",
]
+++

The following will walk through how to create a private amd64 mirror on OpenBSD. We will use httpd and do the mirror syncing via rsync.
<!--more-->

The benefits and purpose of doing this is as an example, your organization may have a lot of OpenBSD machines to install and update so have a local OpenBSD mirror will have speed benefits. This also serves as an excellent learning experience.

Updated 2019-08-18 to reflect the new change for updates to packages on the release version.

Note: The paths below for the OpenBSD version may go out of date but replace the version below with the version you are wanting to create a mirror for and it will work.

----------

* Create the necessary directories:

```
mkdir -p /var/www/pub/OpenBSD/6.8/amd64
mkdir -p /var/www/pub/OpenBSD/6.8/packages/amd64
mkdir -p /var/www/pub/OpenBSD/6.8/packages-stable
mkdir -p /var/www/pub/OpenBSD/patches/6.8/common/
```

* Install the rsync package:

```
pkg_add rsync
```

----------

Download the necessary files from the mirror:

* Download the openbsd/6.8/amd64 directory

```
rsync -rv --progress --delete-delay --delay-updates --fuzzy rsync://mirror.leaseweb.com/openbsd/6.8/amd64 /var/www/pub/OpenBSD/6.8
```

* Download the rest of the openbsd/6.8 contents excluding all arches as we have amd64 already

```
rsync -rv --progress --delete-delay --delay-updates --fuzzy --exclude alpha --exclude arm64 --exclude armv7 --exclude hppa --exclude i386 --exclude landisk --exclude loongson --exclude luna88k --exclude macppc --exclude octeon --exclude sgi --exclude sparc64 --exclude amd64 --exclude packages rsync://mirror.leaseweb.com/openbsd/6.8 /var/www/pub/OpenBSD/
```

* Download the packages from openbsd/6.8/packages/amd64

```
rsync -rv --progress --delete-delay --delay-updates --fuzzy rsync://mirror.leaseweb.com/openbsd/6.8/packages/amd64 /var/www/pub/OpenBSD/6.8/packages
```
    
* Download the updated packages from openbsd/6.8/packages/amd64

```
rsync -rv --progress --delete-delay --delay-updates --fuzzy rsync://mirror.leaseweb.com/openbsd/6.8/packages-stable/amd64 /var/www/pub/OpenBSD/6.8/packages-stable
```

* Download the syspatches for 6.8

```
rsync -rv --progress --delete-delay --delay-updates --fuzzy --exclude arm64 --exclude i386 rsync://mirror.leaseweb.com/openbsd/syspatch/6.8/ /var/www/pub/OpenBSD/syspatch/6.8/
```

Note: replace mirror.leaseweb.com with a  [mirror closest to you.](https://www.openbsd.org/ftp.html "https://www.openbsd.org/ftp.html")

----------

* Edit /etc/httpd.conf

```
# $OpenBSD: httpd.conf,v 1.5 2017/06/26 17:18:57 tb Exp $

# A configuration suitable for being an OpenBSD www/sets mirror.
#
# This assumes you have checked out the www repository under
# /var/www/htdocs/www and that you have a mirror of the OpenBSD
# distribution space mounted under /var/www/pub/OpenBSD, and
# you are running OpenBSD httpd with it chrooting to the default
# /var/www location.
#

prefork 5

# Necessary so patches and other files don't show up as binaries
default type text/plain

server "default" {
		listen on * port 80

		# Optional, but probably best - change your syslog.conf to do
		# what you want with it then.
		log syslog

		# Serve up ftp space mounted in /var/www/pub.
		# Comment this out if you are not mirroring the distribution sets
		location "/pub/*" {
				directory auto index
				log style combined
				root "/"
		}
		# Send man.cgi requests to man.openbsd.org
		location "/cgi-bin/man.cgi*" {
				block return 301 "https://man.openbsd.org$REQUEST_URI"
		}
		# Send cvsweb requests to cvsweb.openbsd.org
		location "/cgi-bin/cvsweb*" {
				block return 301 "https://cvsweb.openbsd.org$REQUEST_URI"
		}
		directory auto index
		root "/htdocs/www"
}

# Include MIME types instead of the built-in ones
types {
		include "/usr/share/misc/mime.types"
		# Necessary to ensure patch files show up as text not binary
		text/plain sig
}
```

* Enable httpd in rc.conf.local

```
rcctl enable httpd
```

* Start httpd

```
rcctl start httpd
```

----------

To have the syspatch and packages-stable directories get updated, you will want a script like the following to run from cron periodically. Here is an example of this.

1. Create /root/mirror-sync.sh

```
#!/bin/sh

rsync -rv --progress --delete-delay --delay-updates --fuzzy rsync://mirror.leaseweb.com/openbsd/6.8/packages-stable/amd64 /var/www/pub/OpenBSD/6.8/packages-stable
rsync -rv --progress --delete-delay --delay-updates --fuzzy --exclude arm64 --exclude i386 rsync://mirror.leaseweb.com/openbsd/syspatch/6.8/ /var/www/pub/OpenBSD/syspatch/6.8/
```

2. Run the script from cron, by running "crontab -u root -e" add the following contents. This will run the update script every 3 days.

```
# OpenBSD mirror: Get latest syspatch and packages-stable updates
0 0 */3 * * /bin/sh /root/mirror-sync.sh
```

----------

References:

- [http://www.openbsd.org/httpd.conf](http://www.openbsd.org/httpd.conf "http://www.openbsd.org/httpd.conf")
- [https://serverfault.com/questions/751659/how-can-i-create-a-private-openbsd-mirror](https://serverfault.com/questions/751659/how-can-i-create-a-private-openbsd-mirror "https://serverfault.com/questions/751659/how-can-i-create-a-private-openbsd-mirror")


