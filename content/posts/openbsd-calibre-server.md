
+++
draft = false
author = "Daulton"
title = "Setup Calibre-web server on OpenBSD"
date = "2020-05-14"
description = "The steps for how to setup a Calibre-web server on OpenBSD."
tags = [
    "openbsd",
    "guides",
]
categories = [
    "guides",
]
+++

I go over how to setup a Calibre-web server on OpenBSD. Calibre-web is a simple Python project and uses a self contained web server even. It perhaps may not be the most secure project around, but why not host all the things on OpenBSD anyway?

<!--more-->

---

1. Install the necessary packages.

```
pkg_add -z python-3.7* py3-pip git
```

2. Create a link so you do not need to remember to type pip3 instead of pip.

```
ln -sf /usr/local/bin/pip3.7 /usr/local/bin/pip

```

3. Clone the calibre-web repo into /usr/local/share/, this will create a directory named calibre-web in that directory.

```
git clone https://github.com/janeczku/calibre-web /usr/local/share/calibre-web
```

4. Change directory into the new calibre-web directory.

```
cd /usr/local/share/calibre-web
```

5. Install the dependencies via pip.

```
pip install --target vendor -r requirements.txt
```

6. For security purposes instead of using the `nobody` user or the `www` user, lets make a separate user that is designated for only running Calibre-web and only able to access its own files.

```
useradd -s /sbin/nologin -d /nonexistent -L daemon -u 447 _calibre-web
```

7. Now that everything is installed, lets change the ownership of these files.

```
chown -R _calibre-web:_calibre-web /usr/local/share/calibre-web
```

8. We need an rc file to start calibre-web, create `/etc/rc.d/calibre-web` with vi or install nano and use that.

```
#!/bin/ksh
#
# $OpenBSD: calibre,v 1.0 2020/05/13 18:49:00 rpe Exp $

daemon="/usr/local/bin/python3 /usr/local/share/calibre-web/cps.py"
daemon_user="_calibre-web"
daemon_timeout="15"
rc_bg=YES

. /etc/rc.d/rc.subr

rc_start() {
        ${rcexec} "${daemon}"
}

rc_cmd $1
```

9. Make the rc file able to execute by adding the execute bit.

```
chmod 555 /etc/rc.d/calibre
```

10. Enable calibre-web to start at boot, and start it now so you can continue with setup then begin using it.

```
rcctl enable calibre
rcctl start calibre
```

---

At this point installation is done and you can now access it, however there is a rather important configuration step left before you can actually use it. It wants you to copy an existing Calibre database (metadata.db), here is a few pointers to help with that.

| Defaults |  |
|--|--|
| URL | http://x.x.x.x:8083 |
| Default username | admin |
| Default password | admin123 |

By default your Calibre Library is made in your home directory unless you specified elsewhere during the first launch setup for Calibre. Basically what we need to do is tar the whole Calibre Library folder and then extract it in the `/usr/local/share/calibre-web` directory on the server.

1. On your desktop in the home directory run the following to create the archive.

```
tar czf calibre-library.tar.gz -C Calibre\ Library/ .
```

2. Transfer the archive from your computer to the server.

```
scp calibre-library.tar.gz your-user@server.com:/tmp
```

3. Create the calibre library directory.

```
mkdir /usr/local/share/calibre-web/calibre-library
```

4. Untar the archive into the new directory.

```
tar xfz /tmp/calibre-library.tar.gz -C "usr/local/share/calibre-web/calibre-library
```

5. Fix the ownership now that new files are copied in.

```
chown -R _calibre-web:_calibre-web /usr/local/share/calibre-web
```

Access the Calibre Web UI again and continue with the configuration, the the following path for the "Location of Calibre Database": `/usr/local/share/calibre-web/calibre-library`

