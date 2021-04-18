+++
author = "Daulton"
title = "Clearing PfSense expired DHCP leases manually"
date = "2018-08-28"
description = "How to clear expired DHCP leases manually in PfSense"
tags = [
    "pfsense",
    "guides",
]
categories = [
    "guides",
]
+++

In PfSense, while when needed expired DHCP leases will be reclaimed, one may want to manually clear expired leases. From DHCP status you can go to '_Show all configred leases_' and click 'Delete lease' one by one, or you can use this method to clear them quicker.
<!--more-->

----------

#### How to

1. Go to Diagnostics then Edit File 
2. Copy the following file path: /var/dhcpd/var/db/dhcpd.leases
3. Click Load
4. Now highlight and delete the blocks of leases you want to delete
5. Click Save when finished
    
#### Source(s)

* [https://forum.pfsense.org/index.php?topic=33577.0](https://forum.pfsense.org/index.php?topic=33577.0 "https://forum.pfsense.org/index.php?topic=33577.0")

