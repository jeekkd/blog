+++
author = "Daulton"
title = "OpenBSD Beginner Essentials"
date = "2018-08-27"
description = ""
tags = [
    "openbsd",
    "notes",
]
categories = [
    "notes",
]
+++

![OpenBSD mascot](/images/openbsd/puffy-logo.png)

I have began learning OpenBSD and before my bookmarks get filled further I am documenting the essential  FAQ, man pages, etc I come across deemed essential for understanding and learning the system. As I am a beginner of the system myself, this is not completely comprehensive or may not be accurate as what is deemed essential to one may not be to another as it is subjective.
<!--more-->

In essence, read the manuals it is very comprehensive.

----------

#### Manuals and FAQs

* [Layout of filesystems](http://man.openbsd.org/hier "http://man.openbsd.org/hier")
 
* [The amd64 boot process](https://www.openbsd.org/faq/faq14.html#BootAmd64 "https://www.openbsd.org/faq/faq14.html#BootAmd64")
    
* [biosboot - amd64-specific first-stage system bootstrap](http://man.openbsd.org/biosboot "http://man.openbsd.org/biosboot")
    
* [amd64 system bootstrapping procedures](http://man.openbsd.org/boot_amd64 "http://man.openbsd.org/boot_amd64")
    
* [amd64-specific second-stage bootstrap](http://man.openbsd.org/boot.8 "http://man.openbsd.org/boot.8")
    
* [init - process control initialization](http://man.openbsd.org/init "http://man.openbsd.org/init")
    
* [rc - command scripts for system startup](http://man.openbsd.org/rc "http://man.openbsd.org/rc")
    
* [Packages and Ports](https://www.openbsd.org/faq/faq15.html "https://www.openbsd.org/faq/faq15.html")
    
* [doas - execute commands as another user](http://man.openbsd.org/doas "http://man.openbsd.org/doas")
    

#### Here is a list of some useful manual pages for new users:

* [From the OpenBSD FAQ 1](https://www.openbsd.org/faq/faq1.html#ManPages "https://www.openbsd.org/faq/faq1.html#ManPages")

* [afterboot(8)](http://man.openbsd.org/afterboot "http://man.openbsd.org/afterboot")  - things to check after the first complete boot.
    
* [help(1)](http://man.openbsd.org/help "http://man.openbsd.org/help")  - help for new users and administrators.
    
* [hier(7)](http://man.openbsd.org/hier "http://man.openbsd.org/hier")  - layout of filesystems.
    
* [man(1)](http://man.openbsd.org/man "http://man.openbsd.org/man")  - display the manual pages.
    
* [adduser(8)](http://man.openbsd.org/adduser "http://man.openbsd.org/adduser")  and  [rmuser(8)](http://man.openbsd.org/rmuser "http://man.openbsd.org/rmuser")  - add or remove new users.
    
* [reboot(8), halt(8)](http://man.openbsd.org/reboot "http://man.openbsd.org/reboot")  and  [shutdown(8)](http://man.openbsd.org/shutdown "http://man.openbsd.org/shutdown")  - stop and restart the system.
    
* [dmesg(8)](http://man.openbsd.org/dmesg "http://man.openbsd.org/dmesg")  - redisplay the kernel boot messages.
    
* [doas(1)](http://man.openbsd.org/doas "http://man.openbsd.org/doas")  - run commands as another user.
    
* [tmux(1)](http://man.openbsd.org/tmux "http://man.openbsd.org/tmux")  - terminal multiplexer.
    
* [ifconfig(8)](http://man.openbsd.org/ifconfig "http://man.openbsd.org/ifconfig")  - configure network interface parameters.
    
* [login.conf(5)](http://man.openbsd.org/login.conf "http://man.openbsd.org/login.conf")  - format of the login class configuration file.
    
* [sendbug(1)](http://man.openbsd.org/sendbug "http://man.openbsd.org/sendbug")  - report a bug you've found.
    

----------

#### Understanding OpenBSD

* [Migrating to OpenBSD](https://www.openbsd.org/faq/faq1.html#OtherUnixes "https://www.openbsd.org/faq/faq1.html#OtherUnixes")
    

----------

#### Confusing things for Linux users

*  _lsblk_  equivalent is  _disklabel -h <target disk>_
    
* After a disk has an MBR partition (using fdisk as an example), use disklabel to create labels to divide up the partition (Similar to LVM kind of)
 
* After labels are created, use newfs to create a filesystem on each of the new labels
    
* The dmesg log is stored under /var/run/ and is called dmesg.boot, it is not under /var/log/
    
* _sudo_  was replaced by  _doas_
    
* DUID (disklabel unique identifier) is similar in concept to a UUID (Universally Unique Identifier). One big difference being that DUIDs refer to whole disks or MBR partitions, rather than a UUID per file system. To refer to an individual disklabel explicitly such as for in fstab, use <DUID>.<label letter> ex. fea9194ee78362d8.a
    
* In place of  _mdadm_, reference  _bioctl_  and  _softraid_  manual pages instead. Additionally, softraid builds its virtual disks out of disklabel partitions

