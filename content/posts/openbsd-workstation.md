+++
author = "Daulton"
title = "My OpenBSD workstation configuration 2018"
date = "2018-08-25"
description = ""
tags = [
    "openbsd",
    "notes",
]
categories = [
    "notes",
]
+++

### My OpenBSD workstation configuration 2018
<!--more-->

* Install XFCE

```
pkg_add -v consolekit2 xfce xfce-extras xscreensaver
```

* Start XFCE at boot

vi /etc/rc.conf.local

```
multicast_host=YES
sshd_flags=NO
xenodm_flags=  
pkg_scripts=messagebus 
ntpd_flags=-s
hotplugd_flags=
apmd_flags="-A"
```

* Then as user add an .xsession file with a line that will start consolekit so that you can shutdown &c from within xfce4.

$ vi .xsession

```
pkill xidle
xidle -delay 5 -sw -program "/usr/X11R6/bin/xlock -mode blank" -timeout 90 &
xset -b
exec ck-launch-session startxfce4
```
    
* If using IPv6, do the following to enable the slaacd daemon

```
rcctl enable slaacd
rcctl start slaacd
```

* Prevent xconsole from starting at every login, edit /etc/X11/xenodm/Xsetup_0 and do the following

```
xconsole -geometry 480x130-0-0 -daemon -notify -verbose -fn fixed -exitOnFail
sleep 0.1
```

* Install programs

```
pkg_add -v firefox geany keepassxc openvpn libreoffice hexchat vlc uget vim youtube-dl ubuntu-fonts gimp filezilla galculator tilda evince wget redshift pelican ansible rsync pycharm htop gnupg sshpass ansible
```

* Configure /etc/pf.conf

```
extif = re0

set block-policy drop
set skip on lo0
match in all scrub (no-df random-id max-mss 1440)
antispoof quick for (egress)
block in quick on egress from { no-route urpf-failed } to any
block in all

# Allow receiving ipv6 types
icmp6types = "{128, 133, 134, 135, 136, 137}"
pass in quick on $extif inet6 proto ipv6-icmp icmp6-type $icmp6types keep state

pass out quick inet keep state
pass out quick inet6 keep state
```
     
* Enable IPv6 in /etc/hostname.re0. Note that yours may not be re0 as I have a realtek NIC

```
inet6 autoconf autoconfprivacy soii
```

* Edit /etc/sysctl.conf

```                                          
machdep.allowaperture=1
kern.nosuidcoredump=1
kern.shminfo.shmall=32768
hw.smt=1
```

* Configure ntpd

```
vi /etc/ntpd.conf and edit server to router LAN interface IP
```
   
* Configure suspend. Create /etc/apm/suspend and make it executable:

```
$ cat /etc/apm/suspend
#!/bin/sh
pkill -USR1 xidle
```

* Edit maximum user process memory limits in /etc/login.conf

```
datasize-max=1024M
datasize-cur=1024M
```

* Setup crontab
    
```
# Daily backup, at 7 PM
0 19 * * * sh /home/daulton/Files/Scripts/backup.sh >/dev/null 2>&1
```

* Audio

Reminder to disable internal audio to make using USB DAC easier, since internal audio   is the primary audio device instead of messing trying to change the default device

* Configure /etc/doas.conf

```
permit persist :wheel
permit nopass keepenv root
```

* Ports

```
$ cd /tmp
$ ftp https://ftp.openbsd.org/pub/OpenBSD/$(uname -r)/{ports.tar.gz,SHA256.sig}
$ signify -Cp /etc/signify/openbsd-$(uname -r | cut -c 1,3)-base.pub -x SHA256.sig ports.tar.gz
```

You want to untar this file in the /usr directory, which will create /usr/ports and all the directories under it.

```    
cd /usr
tar xzf /tmp/ports.tar.gz
```

* Install ntfs-3g if wanted:

```
cd /usr/ports/sysutils/ntfs-3g
make install
make clean
```

Note: To mount the disk in fstab use disklabel sd<number> to find the DUID

ntfs-3g example:

```
ntfs-3g /dev/sd1i /mnt/data/
```
      
and to mount at boot add the command to /etc/rc.local
       
```
/usr/local/bin/ntfsfix /dev/sd1i && /usr/local/bin/ntfs-3g /dev/sd1i /mnt/data/
```

----


## Links: ##

* http://www.eradman.com/posts/openbsd-workstation.html
* http://sohcahtoa.org.uk/openbsd.html
* https://www.openbsd.org/faq/ports/ports.html
* https://balu-wiki.readthedocs.io/en/latest/security/openbsd.html

