+++
author = "Daulton"
title = "My OpenBSD desktop experience"
date = "2018-10-21"
description = "My observations about using OpenBSD as a desktop system."
tags = [
    "openbsd",
    "blog",
]
categories = [
    "blog",
]
+++

I have came from the Linux side of things, having used predominately Debian and then moving to Hardened Gentoo for some time. After the [Grsecurity security enhancing patchset](https://grsecurity.net/) was made commercial only via subscription I made the shift from Gentoo to OpenBSD for my desktop to continue being able to use a secure system on the desktop. These are my thoughts from a Linux and sometimes Gentoo specific perspective about having used OpenBSD [as a desktop for a year](https://www.gentoo.org/support/news-items/2017-08-19-hardened-sources-removal.html).
<!--more-->

Coming from Gentoo and moving to OpenBSD was not all that difficult. It is a system that I highly recommend other Gentoo users, even if they do not use it on their desktops, to try it as a server. For a server platform it is fantastic and I personally use it as my go-to server platform and have switched a number of mine over. However as always choose what makes the most sense for what you are doing, where you are deploying it, etc.


## Observations

 - Updates are easy with pkg_add -u and syspatch
 - System upgrades are easy to do from version to version. The upgrade FAQ notes are great in that they detail all that you need to do and what has significantly changed so you can be aware, change config files if necessary, etc.
	 - The rolling release model was nice that there was never a need to upgrade version to version in a manner such as OpenBSD. Although this can be remedied by using snapshots or mtier.

## Pros

- The text based installer is great, very straight forward and simple. Installation and upgrades of the whole system are quick.
	- I personally do not like installers such as Anaconda which Fedora, Red hat, CentOS, etc, use. I lean more toward the styles of how OpenBSD, Debian, OpenIndiana, etc, do it.
- OpenBSD is the home of OpenNTP, OpenSSH, LibreSSL, httpd, doas, etc, so to use the system where all of this great software is native and first upgraded here is swell.
- Native and built in security enhancements, there is no need to rely on a third party such as Grsecurity to provide security that should already be there in the first place.
- System stability is great.
- Very little setup is required to get a full fledged desktop system up and running and then on going maintenance is minimal.
	- Its very nice to not have to deal with slot conflictions when updating Gentoo and such anymore.
- By default a more secure version of the X server is used called Xenocara. On Gentoo in the wiki there are instructions of how to do things such as run it as another user, etc, but for it be done by default is nice.
- The manual pages and FAQ are very well done, using them is actually helpful and for a nice amount of manual pages there are examples.
- Pf is the system firewall, I consider this a big plus after using it and it makes iptables look like a gigantic mess.
- The system as a whole feels very consistent and well thought out, this becomes apparent as you start using OpenBSD base system daemons where the config file syntax is even similar between the various daemons. 
- The RC system is very simple and easy to use, I liked OpenRC but its a joy to not have anything like systemd around!
	- The commands surrounding it are very straight forward (rcctl) and the configuration files (/etc/rc.conf and /etc/rc.conf.local) are simple.
- - BSD licensing vs GNU licensing
- Documentation is considered as important as code.
- Whenever a decision has to be made, security and correctness is prioritized VS general-purpose and popularity and efficiency.
- OpenBSD has excellent man pages with practical examples. Use man. Really.
- Proactive security: W^X, ipsec, ASLR, kernel relinking, RETGUARD, pledge, unveil, etc.

## Cons

- Missing KDE Plasma 5 desktop, I personally really love this release of KDE and thought it was a fantastic desktop environment. In various parts of my Linux usage I have also used XFCE, so going back to that is not so bad but having KDE 5 available would be a massive plus.
- OpenBSD in general is a bit slower, this is not really all that big of a con since there is not a drastic speed difference while I use the system for my needs (coding, browsing, media streaming, remote server work, etc). Because of the focus on security and simplicity, and not on speed or optimizations, software runs a bit slower than on Linux. In my experience (and in some benchmarks) about 10%-20% slower.
- Slow FUSE performance for mounted NTFS drives. Again not a big con since I do not use this NTFS drive much, but is a con and an observation to note.
- [I have had Chromium crash for a handful of sites](https://www.reddit.com/r/openbsd/comments/8kvvjf/openbsd_63_chromium_and_googledrive_coredump/), I will need to try Chromium again as of the 6.4 release however for 6.3 I had to result to using Iridium for my general use browser to get around this issue. 
- Lack of ACL and XATTR.
- Lack of advanced file system(s), makes use of OpenBSD for NAS applications difficult or undesirable.
- As a project and as a community OpenBSD does not have a mindset of valuing guides for how to do and setup specific things, I have found this nature of information sparse. Coming from Gentoo where there is the Gentoo wiki, or being able to reference the Arch wiki or find some information from a DigitalOcean tutorial, etc. that change has been felt and it makes things more difficult as you find yourself needing to learn what others already know but have not cared to share.
	- However some packages provide configuration and other information in a file located in `/usr/local/share/doc/pkg-readmes` and this helps a great deal when dealing with things such as Postgres or Zabbix, etc.
