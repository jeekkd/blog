+++
author = "Daulton"
title = "Realtek nics on vmware exsi 6.7"
date = "2018-08-28"
description = "This guide will help you install the drivers for Realtek 8168/8111/8411/8118 based NICs on exsi 6.7"
tags = [
    "guides",
]
categories = [
    "guides",
]
+++

### Realtek 8168/8111/8411/8118 based NICs on Vmware EXSI 6.7
<!--more-->

----------

This guide will help you install the drivers for Realtek 8168/8111/8411/8118 based NICs so you can use those network cards on vmware exsi 6.7 hosts.

Googling you may have found the net51-drivers package, or even tried it and had it fail, but that is out of date for esxi 6.7.

Use the following:

- [net55-r8168](https://vibsdepot.v-front.de/wiki/index.php/Net55-r8168 "https://vibsdepot.v-front.de/wiki/index.php/Net55-r8168")
    
- [net51-r8169](https://vibsdepot.v-front.de/wiki/index.php/Net51-r8169 "https://vibsdepot.v-front.de/wiki/index.php/Net51-r8169")
    
- [net51-sky2](https://vibsdepot.v-front.de/wiki/index.php/Net51-sky2 "https://vibsdepot.v-front.de/wiki/index.php/Net51-sky2")
    

----------

#### Online installation

If your host is already installed and has a direct Internet connection then you can install it from an ESXi shell by running the following commands:

```
esxcli software acceptance set -level=CommunitySupported

esxcli network firewall ruleset set -e true -r httpClient

esxcli software vib install -n net55-r8168 -d http://vibsdepot.v-front.de
esxcli software vib install -n net55-r8169 -d http://vibsdepot.v-front.de
esxcli software vib install -n net55-sky2 -d http://vibsdepot.v-front.de
```

----------

#### Offline installation

If you do not have network connectivity then you can use the available offline bundles from the above links.

1. unzip the bundle on your computer.
    
2. move/copy the .vib file to any location on the esxi. (for example /tmp)
    
3. run the esxcli command with complete path to the vib file.
  
```
esxcli software vib install -v /tmp/xxxxxxxxxxx.vib
```

4. If a reboot is required, reboot the esxi host.

