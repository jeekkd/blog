+++
author = "Daulton"
title = "My Homelab"
date = "2018-08-27"
lastmod = "2020-04-22"
description = "Details about my homelab, check it out!"
tags = [
    "homelab",
]
categories = [
    "blog",
    "homelab",
    "portfolio",
]
+++

The hands-on portion of my studying is done within a homelab I have put together after graduating college so I may continue to learn and expand my knowledge.
<!--more-->

This serves my needs for the educational aspect, and practical self hosting for uses such as [NextCloud](https://nextcloud.com/ "https://nextcloud.com/")  ([formly OwnCloud](https://en.wikipedia.org/wiki/Nextcloud#History_of_the_fork "https://en.wikipedia.org/wiki/Nextcloud#History_of_the_fork")) hosting. Hands-on practice is important, learning how to update, configure, manage, secure, etc systems takes a lot of learning. A lot of this can be learned on the job, and you're prepared for it some through formal education, but this hands on practice equates to experience. It may not be real world experience, unless you are running some public services perhaps, but it is valuable nonetheless.

----------

![Network diagram](/images/homelab/server_diagram.png)

As pictured above, my lab consists of:

* A [PC Engines apu2c2 board](http://www.pcengines.ch/apu2c2.htm "http://www.pcengines.ch/apu2c2.htm")  running  [PfSense](https://pfsense.org/ "https://pfsense.org/")
* [HP Procurve 1800-24G managed switch](https://h10057.www1.hp.com/ecomcat/hpcatalog/specs/provisioner/05/J9028B.htm "https://h10057.www1.hp.com/ecomcat/hpcatalog/specs/provisioner/05/J9028B.htm")
* Two servers running  [Proxmox hypervisor](https://www.proxmox.com/en/ "https://www.proxmox.com/en/")
* Storage server running  [FreeNAS](http://www.freenas.org/ "http://www.freenas.org/")
* D-Link DIR-825 WiFi AP running [OpenWRT](https://openwrt.org/ "https://openwrt.org/")
* The PBX I am using is FreePBX with a Grandstream 1625 deskphone
    

----------

#### PfSense

On the PfSense router the following services are configured to serve my home network:

* DHCP
* DNS
* NTP
* Snort IPS running on the WAN link
* OpenVPN configured as a client
* Pf firewall
* OpenVPN server VPN to connect to my NextCloud instance remotely in a secure fashion. My IP is not static although it does not change often, so I use a dynamic DNS service so I can always connect to my OpenVPN server.
    

----------

#### Virtualization Server

At one point I was using vSphere for my hypervisor but I switched to [ProxMox](https://www.proxmox.com/en/ "https://www.proxmox.com/en/") due to the integration of essential features into the base system. Things such as clustering, HA, automated backups, easier updating, etc. without needing additional VMWare products was quite nice and very benefical for a homelab setting. My important virtual machines were backed up using [ghettoVCB](https://communities.vmware.com/docs/DOC-8760 "https://communities.vmware.com/docs/DOC-8760"), a free vmware community developed script for backups. It is simple, robust, and does the job. It was done over NFS to my storage server. I had used vSphere and vCenter long enough to get a grasp on the platform because it was common to find it in Enterprise business environments so being familiar with it is important.

At this point I have two virtualization servers, both of which are ProxMox. One is a custom built server on commodity hardware and it has an AMD FX-8300 Eight-Core Processor and 16 GB of RAM. The other is an IBM x3650 M2 with 2x Intel(R) Xeon E5530 and 64 GB of RAM.

----------

#### Storage Server

A [FreeNAS](http://www.freenas.org/ "http://www.freenas.org/")  box which contains my media and backups, and runs some services in [Jails](https://www.freebsd.org/doc/handbook/jails.html "https://www.freebsd.org/doc/handbook/jails.html").

I have two pools setup, one for media, and another for everything else. The first pool is raidz1 and the other is a mirror. The media pool only has one dataset since its not split up as that is not necessary, while the other pool has various datasets such as for vmware backups, jails, my backups, etc.

FreeNAS has been great, its been capable of everything I've wanted and has been stable. Sticking to the Stable train has been best, and I've waited to upgrade after each update and version. I waited about two months for the issues to be ironed out with the upgrade, so going from 9.10.x to 11.x was even easy as an example.

