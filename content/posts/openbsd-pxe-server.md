+++
author = "Daulton"
title = "Setup OpenBSD PXE server"
date = "2018-08-28"
description = "I will go over creating a tftp server to PXE boot from to do autoinstalls."
tags = [
    "openbsd",
    "guides",
]
categories = [
    "guides",
]
+++

We will be going over creating a tftp server to PXE boot from to do autoinstalls. DHCP server configuration will not be gone over, you will need to edit whichever DHCP server you are using separately but please note you do need to do some setup there as well. Search for what to set for your DHCP server, but for pfSense in the DHCP server options I just need to set 'Next server' to the PXE server IP address and 'Default BIOS file name' to the value of pxeboot.
<!--more-->

Please note for the pxe-prepare script you do not need to be running a full mirror, point the WEBDIR variable in the script below to reflect the location you have pxeboot,bsd.rd,INSTALL.amd64 (or INSTALL.i386) stored.

Note: The paths below for the OpenBSD version may go out of date but replace the version below with the version you are wanting to PXE boot and it will work.

1. Create the required directory and download the necessary bits

```
mkdir -p /var/www/pub/OpenBSD/6.5/amd64
cd /var/www/pub/OpenBSD/6.5/amd64
wget https://mirror.leaseweb.com/pub/OpenBSD/6.5/amd64/pxeboot
wget https://mirror.leaseweb.com/pub/OpenBSD/6.5/amd64/bsd.rd
wget https://mirror.leaseweb.com/pub/OpenBSD/6.5/amd64/install.amd64
```

2. Then set WEBDIR like so in the below script:

```
/var/www/pub/OpenBSD/6.5/${ARCH}
```

3. Run the pxe-boot-prepare script

```
#!/bin/sh
# (c) J65nko daemonforums.org
# ISC license
#
# ---- prepare OpenBSD box as PXE boot server
# See http://www.openbsd.org/faq/faq6.html#PXE for the details
# If you use an 'install.conf' file for autoinstall(8) read that 
# man page for additional instructions on configuring the DHCP server

if [ "$(id -u)" -ne  0 ]; then 
	echo $0 error:  Requires root privilege, sorry, bailing out .... 
	exit 10 
fi

case "$1" in
amd64 | i386 )  ARCH="$1"
				 ;;
* )             echo "$0 : Please specify architecture ('amd64' or 'i386')" 
				exit 1
				 ;;
esac

# tftpboot is a dyslexic nightmare so we select another name here ....

PXE_DIR=/pxe
WEBDIR=/var/www/pub/OpenBSD/6.5/${ARCH}
COM_SPEED=19200

echo Creating ${PXE_DIR}/etc ...
mkdir -p ${PXE_DIR}/etc

# --- enable tftpd daemon in /etc/rc.conf.local

FILE=/etc/rc.conf.local

echo Checking for 'tftpd_flags' setting in "${FILE}" ...

if grep 'tftpd_flags=' ${FILE} ; then
   echo Trivial File Protocol Daemon  already mentioned in "${FILE}" 
   echo So please check it .... 
else 
   echo Updating ${FILE} to enable TFTP daemon..
   cat <<-END >>${FILE}
		# --- $(date) ---
		#tftpd_flags=NO          # for normal use: "[chroot dir]
		tftpd_flags=${PXE_DIR}
END
fi

echo "Creating ${PXE_DIR}/etc/random.seed for bootloader ..."
# -- code lifted from /etc/rc
#dd if=/dev/random of=${PXE_DIR}/etc/random.seed bs=512 count=1 status=none
dd if=/dev/random of=${PXE_DIR}/etc/random.seed bs=512 count=1 
chmod 644 ${PXE_DIR}/etc/random.seed

# See boot.conf(8) for the details 
 
FILE=${PXE_DIR}/etc/boot.conf
#FILE=$(basename ${FILE})

echo Creating ${FILE} ...
cat <<END >${FILE}
time
set image bsd.rd
END

echo Copying  'pxeboot', 'bsd.rd' and "INSTALL.${ARCH}" from ${WEBDIR} ....
# INSTALL.${ARCH} is not needed for PXE booting
# we use it only  as indicator for architecture

cp -p ${WEBDIR}/{pxeboot,bsd.rd,INSTALL.${ARCH}} ${PXE_DIR}

# -- for autoinstall(8). Ssee NOTE at end of script
# Not harmful  if you don't use autoinstall

echo "For autoinstall(8) creating symbolic link "${PXE_DIR}/auto_install" \
pointing to "${PXE_DIR}/pxeboot" ..."
ln -sf pxeboot ${PXE_DIR}/auto_install 


cat <<END
------- contents of ${PXE_DIR} -----------
$(ls -lR ${PXE_DIR})
--- contents of ${PXE_DIR}/etc/boot.conf --
$(cat ${PXE_DIR}/etc/boot.conf)
--------------------------------------
END
```


4. Create htdocs/www directory

```
mkdir -p /var/www/htdocs/www
```

5. Enable httpd in /etc/rc.conf.local

```
rcctl enable httpd
```

6. Add the httpd config at /etc/httpd.conf

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

prefork 3

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

7. Start httpd

```
rcctl start httpd
```

8. Create /var/www/htdocs/www/install.conf

The great thing about autoinstall is the install.conf file answers the prompt very plainly making the configuration file so easy and straight forward. The answers file contains strings which match the questions from the installer, such as "Unable to connect using https. Use http instead?" can be entered and answered with an equals sign then yes such as " = yes". That's all!

```
Choose your keyboard layout = en
System hostname = obsb
Password for root = xxxxxxxxxxxx
Change the default console to com0 = no
Setup a user = daulton
Password for user = xxxxxxxxxxxx
Public ssh key for user = ssh-ed25519 XYZ123... daulton@daulton.ca
What timezone are you in = Canada/Eastern
network interfaces = em0
IPv4 address for em0 = dhcp
Location of sets = http
HTTP Server = 10.100.3.30
Unable to connect using https. Use http instead? = yes
Set name(s) = -g* -x* +xb*
```

Note: If you don't specify a line then a default will be used.

----------

References:

* [https://man.openbsd.org/autoinstall](https://man.openbsd.org/autoinstall "https://man.openbsd.org/autoinstall")
    
* [http://daemonforums.org/showthread.php?t=8825](http://daemonforums.org/showthread.php?t=8825 "http://daemonforums.org/showthread.php?t=8825")
    
* [http://daemonforums.org/showthread.php?t=8847](http://daemonforums.org/showthread.php?t=8847 "http://daemonforums.org/showthread.php?t=8847")
    
* [http://eradman.com/posts/autoinstall-openbsd.html](http://eradman.com/posts/autoinstall-openbsd.html "http://eradman.com/posts/autoinstall-openbsd.html")
    
* [http://www.openbsd.org/faq/faq4.html#site](http://www.openbsd.org/faq/faq4.html#site "http://www.openbsd.org/faq/faq4.html#site")

