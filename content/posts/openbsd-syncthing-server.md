+++
draft = false
author = "Daulton"
title = "Syncthing server setup on OpenBSD"
date = "2020-09-29"
lastmod = "2021-01-10"
description = "I go over how to setup a Syncthing server on OpenBSD."
tags = [
    "openbsd",
    "guides",
]
categories = [
    "guides",
]
+++

I go over how to setup a [Syncthing](https://syncthing.net/) server on OpenBSD. [Syncthing](https://syncthing.net/) is a continuous file synchronization program. It synchronizes files between two or more computers in real time, safely protected from prying eyes. 

<!--more-->

These steps should apply to OpenBSD 6.7 or greater.

---

1. Install the Syncthing package.

```
pkg_add syncthing
```

2. Enable Syncthing to start at boot.

```
rcctl enable syncthing
```

3. We need to increase the maximum open files that Syncthing can use, this is especially needed during initial sync where a lot of data is getting transferred at once. Add the following to the bottom of the config.

> vi /etc/login.conf

```
syncthing:\
        :openfiles-max=60000:\ 
        :tc=daemon:
```

4. Run the following command to generate the binary format of the login.conf file.

```
cap_mkdb /etc/login.conf
```

5. OpenBSD also stores a kernel-level file descriptor limit in the sysctl variable kern.maxfiles. Increase it from the default of 7030 to 16000 so we do not run out of openfiles and make the system unusable.

```
echo "kern.maxfiles=80000" >> /etc/sysctl.conf
sysctl kern.maxfiles=80000
```

6. Run the following to immediately have the maximum open files increased instead of waiting until it occurs at reboot. The reason this amount is higher than in step 3 is because the login.conf change applies only to what Syncthing can use, but using the sysctl increases the overall amount.

```
sysctl kern.maxfiles=80000
```

7. Edit pf.conf to allow access to Syncthing, edit networks_sync to be the network syncing will occur from and edit management to contain the management machine IP which would be your workstation. By allowing the web interface to only be accessed by yourself it adds a small layer of extra security. Edit ext_if to be your network interface, check with ifconfig.

```
ext_if = em0

# ----- DEFINITIONS -----
 
# Reassemble fragments
set reassemble yes
 
# Return ICMP for dropped packets
set block-policy return
 
# Enable logging on egress interface
set loginterface egress

set limit table-entries 1000000
set ruleset-optimization basic
 
# Allow all on Loopback interface
set skip on lo
 
# Define ICMP message types to let in
icmp_types = "{ 0, 8, 3, 4, 11, 30 }"

table <management> { 10.30.3.10 }
table <networks_sync> { 10.30.3.0/24 10.30.1.0/24 }

# ----- INBOUND RULES -----
# Scrub packets of weirdness
match in all scrub (no-df max-mss 1440)
match out all scrub (no-df max-mss 1440)
 
# Drop urpf-failed packets, add label uRPF
block in quick log from urpf-failed label uRPF
block quick log from <fail2ban>

# Security enhancements
block in from no-route to any
block in from urpf-failed to any
block in quick on $ext_if from any to 255.255.255.255
antispoof for $ext_if
block log all

# Pass in without restriction or rate limiting whitelsited IPs
pass in quick inet proto tcp from <management> to any

# HTTP
pass in quick on $ext_if inet proto tcp from <management> to $ext_if port { 8384 }

# SyncThing
pass in quick on $ext_if inet proto tcp from <networks_sync> to $ext_if port { 22000 }
pass in quick on $ext_if inet proto udp from <networks_sync> to $ext_if port { 21027 }

# ICMP
pass in quick inet proto icmp icmp-type $icmp_types
pass in quick inet6 proto icmp6 

# SSH
pass in quick proto tcp from <management> \
    to port { 22 } \
    flags S/SA modulate state \
    (max-src-conn 5, max-src-conn-rate 5/5, overload <fail2ban> flush global)

# ----- ALL OTHER TRAFFIC TO BE DROPPED -----
 
#block in quick log on egress all
block quick proto tcp from <fail2ban>
 
# ----- OUTBOUND TRAFFIC -----
 
pass out quick on egress proto tcp from any to any modulate state
pass out quick on egress proto udp from any to any keep state
pass out quick on egress proto icmp from any to any keep state
pass out quick on egress proto icmp6 from any to any keep state
```

8. Reload pf to load the new configuration.

```
pfctl -f /etc/pf.conf
```

9. Now we are ready to start the Syncthing daemon.

```
rcctl start syncthing
```

10. You can now connect to the web interface at http://x.y.x.y:8384/

----

Notes

- Make sure you enable "Use HTTPS for GUI" and setup a GUI Authentication User.
- People say "Watch for Changes" does not work on OpenBSD but I have not had an issue with it so far.
- If Syncthing gives an error about too many open files increase maxfiles further.
