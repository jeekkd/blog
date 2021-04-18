+++
draft = false
author = "Daulton"
title = "GoatCounter server setup on OpenBSD"
date = "2021-01-04"
description = "How to setup the GoatCounter server on OpenBSD so you can count goats! Sweet!"
tags = [
    "openbsd",
    "guides",
]
categories = [
    "guides",
]
+++

[GoatCounter](https://github.com/zgoat/goatcounter) is an open source web analytics platform available as a hosted service (free for non-commercial use) or self-hosted app. It aims to offer easy to use and meaningful privacy-friendly web analytics as an alternative to Google Analytics or Matomo.

I am going to walk you through running GoatCounter on OpenBSD. This approach utilizes the built in Let's Encrypt and SQLite support, so extra setup pertaining to certs or a larger relational database system like Postgres is not necessary.

This guide was made on OpenBSD 6.8. No steps should change between OpenBSD versions.

<!--more-->

---

1. Install Go and Git so we can later clone the Github repo and then build the GoatCounter binary.

```
pkg_add -z go git
```

2. Compile from Go source with the following. If an OpenBSD build gets released officially this step wont be necessary.

```
git clone -b release-1.4 https://github.com/zgoat/goatcounter.git
cd goatcounter
go build -ldflags="-X main.version=$(git log -n1 --format='%h_%cI')" \
    -tags osusergo,netgo,sqlite_omit_load_extension \
    -ldflags='-extldflags=-static' \
    ./cmd/goatcounter
```

3. Copy GoatCounter binary to correct path in hierarchy. `man heir` says local executables, libraries, etc. go here.

```
cp goatcounter /usr/local/bin
```

4. Make the binary executable by adding the execute bit.

```
chmod +x /usr/local/bin/goatcounter
```

5. Make the database directory. It is probably okay to put the database here.

```
mkdir /var/goatcounter
```

6. Create daemon user for GoatCounter, this is also needed for directory permissions. By running the service as its own low privilege user it helps to isolate it, and protect the rest of the system. 

```
useradd -s /sbin/nologin -d /nonexistent -L daemon -u 448 _goatcounter
```

7. Set directory ownership and permissions for the database directory. Once we run GoatCounter a SQLite database will be created in this directory.

```
chown -R _goatcounter:_goatcounter /var/goatcounter
chmod -R 770 /var/goatcounter/
```

8. Create the rc file to enable starting GoatCounter at boot.

> vi /etc/rc.d/goatcounter

```
#!/bin/ksh
#
# $OpenBSD: goatcounter,v 1.0 2020/12/27 17:49:00 rpe Exp $

daemon="/usr/local/bin/goatcounter"
daemon_flags="serve -db 'sqlite:///var/goatcounter/goatcounter.sqlite3' &> /dev/null"
daemon_user="_goatcounter"
rc_reload=NO
rc_bg=YES

. /etc/rc.d/rc.subr

pexp="/usr/local/bin/goatcounter.*"

rc_start() {
        ${rcexec} "${daemon} ${daemon_flags}"
}

rc_stop() {
        pkill -xf "${pexp}"
}

rc_restart() {
        pkill -xf "^${pexp}"
        ${rcexec} "${daemon} ${daemon_flags}"
}

rc_check() {
        pgrep -q -xf ${pexp}
}

rc_cmd $1
```

9. Set the permissions for the rc script.

```
chmod 555 /etc/rc.d/goatcounter
```

10. Allow traffic to pass through the firewall to GoatCounter. Change `ext_if` to be your own network interface, check with `ifconfig`. You can use your own pf rules if you like, just make sure port 443 and 80 are open.

> vi /etc/pf.conf

```
ext_if = em0

# ----- DEFINITIONS -----
 
# Reassemble fragments
set reassemble yes
 
# Return ICMP for dropped packets
set block-policy return
 
# Enable logging on egress interface
set loginterface egress

set ruleset-optimization basic
 
# Allow all on Loopback interface
set skip on lo
 
# Tables
table <banned>
 
# Define ICMP message types to let in
icmp_types = "{ 0, 8, 3, 4, 11, 30 }"

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

# HTTP(s)
pass in quick on $ext_if inet proto tcp from any to $ext_if port { 443 80 }

# ICMP
pass in quick inet proto icmp icmp-type $icmp_types
pass in quick inet6 proto icmp6 

# SSH
pass in quick proto tcp from any \
    to port { 22 } \
    flags S/SA modulate state \
    (max-src-conn 5, max-src-conn-rate 5/5, overload <banned> flush global)

# ----- ALL OTHER TRAFFIC TO BE DROPPED -----
 
#block in quick log on egress all
block quick proto tcp from <banned>
 
# ----- OUTBOUND TRAFFIC -----
 
pass out quick on egress proto tcp from any to any modulate state
pass out quick on egress proto udp from any to any keep state
pass out quick on egress proto icmp from any to any keep state
pass out quick on egress proto icmp6 from any to any keep state
```

11. Load the new pf configuration.

```
pfctl -f /etc/pf.conf
```

12. Enable the daemon to start at boot.

```
rcctl enable goatcounter
```

13. Start GoatCounter now.

```
rcctl start goatcounter
rcctl check goatcounter
```

14. Create your site now. Note that this must be resolvable, if this is for local development edit your /etc/hosts file.

```
goatcounter create -email me@example.com -domain stats.example.com -db 'sqlite:///var/goatcounter/goatcounter.sqlite3'
```

15. Now you are finished and can access your site at the same domain as above.

----

Notes

- If you are having problems start goatcounter with rcctl -d start .. and if you see "Memstore.Init: delete DB store: attempt to write a readonly database" then re-apply the ownership for /var/goatcounter again.
- Make sure you have DNS setup so you can resolve the domain listed in step 13.
- Refer to the [repo](https://github.com/zgoat/goatcounter) for details.
- You can uninstall Go and Git afterwards if you like, but you will need them for when you update Goatcounter.
- Do not forget to backup /var/goatcounter/goatcounter.sqlite3.
- For local development or testing where you want this network accessible, try the following flags `-listen :8081 -port 8081 -tls none`

