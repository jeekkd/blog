+++
author = "Daulton"
title = "Setup Meshcentral server on OpenBSD"
date = "2019-03-14"
description = "This guide will help you set up a MeshCentral server on OpenBSD."
tags = [
    "openbsd",
    "guides",
]
categories = [
    "guides",
]
aliases = [
    "/meshcentral-server-on-openbsd/",
]
+++

This guide will help you set up a [MeshCentral server](https://www.meshcommander.com/meshcentral2) on OpenBSD 6.5. 
<!--more-->

Connect to your home or office devices from anywhere in the world using MeshCentral, the open source, remote monitoring and management server. Once installed, each enabled computer will show up in the "My Devices" section of the web site and will be able to perform remote desktop, remote terminal, file transfers and more. 

----------

1. Install the Mongodb package. 

```
pkg_add mongodb
```  
 
2. Start and enable Mongodb at boot.

```
rcctl start mongod
rcctl enable mongod
```

3. Temporary remount /usr with wxallowed while we compile the port. However, for cloud VPS (Vultr for example) the installations usually only a root partition instead of how OpenBSD splits it up by default, you will need to edit /etc/fstab and add wxallowed to the options for the root partition and then reboot. Assure to remove this from the fstab options after you are done.
   
```     
mount -r -o wxallowed /usr/     
```
      
4. Install NodeJs from ports as it is not available by a package.

```
$ cd /tmp
$ ftp https://cdn.openbsd.org/pub/OpenBSD/$(uname -r)/{ports.tar.gz,SHA256.sig}
# cd /usr
# tar xzf /tmp/ports.tar.gz
# cd /usr/ports/lang/node
# make install
# make clean 
```      
        
5. Create the MeshCentral user. The parameters used here are important as we will not let this user login, it has no home directory, and its class is set to daemon. In line with the OpenBSD daemon user naming scheme, we preface the username with an underscore (_) to make it easily identifiable as a daemon user. 

```
useradd -s /sbin/nologin -d /nonexistent -L daemon -u 446 _meshcentral
```     
 
6. Lets install MeshCentral and adjust the permissions.

```
mkdir -p /usr/local/meshcentral
cd /usr/local/meshcentral
npm install meshcentral
npm install otplib
npm install mongojs
chown -R _meshcentral:_meshcentral /usr/local/meshcentral
```
     
7. To prepare for the next step we need a /etc/doas.conf to allow root to run commands as _meshcenteal with no password. Create /etc/doas.conf with the following contents and save it.
 
```
permit nopass root as _meshcentral             
```

8. Configuring for MongoDB and adjusting some other settings such as the network port. Open up the following config in an editor then, make the start of the file look like below. If the setting does not exist yet, just add it below one of the ones we are adjusting in the main settings block. 

If you start with the default config.json created by MeshCentral, you will need to remove some underscore character in front of settings to enable the setting, such as mongodb and wanonly. You can also add an underscore to other values. For details on all of the config.json options, including the “WANonly” option, refer to the MeshCentral User’s Guide.
    
Before you can edit the configuration, start the Meshcentral briefly so it generates the default configurations and certificates. Once you see that it says "MeshCentral HTTPS server running...", Ctrl-C to exit then edit the configuration file next.
   
``` 
cd /usr/local/meshcentral/node_modules/meshcentral/ && doas -u _meshcentral /usr/local/bin/node /usr/local/meshcentral/node_modules/meshcentral/meshcentral.js --launch
```

Now edit config.json

> vi /usr/local/meshcentral/meshcentral-data/config.json

Edit the following values

```
{
"settings": {
		"Cert": "meshcentral.example.com",
		"MongoDb": "mongodb://127.0.0.1:27017/meshcentral",
		"WANonly": true,
		"Port": 3000,
		"ExactPorts": true,
		"RedirPort": 3001,
		"AllowLoginToken": true,
		"AllowFraming": true,
		"NewAccounts": false,
},
…
}
```

9. Adjust the permissions again as we ran Meshcentral and it generated new files we need to change the ownership of. 
 
```
chown -R _meshcentral:_meshcentral /usr/local/meshcentral
```

10. Add the following to the root crontab to start MeshCentral at boot. Edit the root crontab by doing the following command as root: crontab -e 

```
@reboot cd /usr/local/meshcentral/node_modules/meshcentral/ && doas -u _meshcentral /usr/local/bin/node /usr/local/meshcentral/node_modules/meshcentral/meshcentral.js --launch
```

11. This is a reference /etc/pf.conf for you to keep your server secure. Add any locally connected networks which should have access and any public IP address of a network which will have client PCs connect from to target_whitelist table. Add your own home and/or business IP to my_own_IPs table. Lastly, replace ext_if with the name of your external network interface, check with the ifconfig command what yours is.

```
ext_if = vio0
set reassemble yes
set block-policy return
set loginterface egress
set ruleset-optimization basic
set skip on lo

icmp_types = "{ 0, 8, 3, 4, 11, 30 }"

table <target_whitelist> const { 45.63.15.84, 10.18.5.0/24 }
table <my_own_IPs> const { 45.63.15.84 }
table <bruteforce>

match in all scrub (no-df max-mss 1440)
match out all scrub (no-df max-mss 1440)

block in quick log from urpf-failed label uRPF

block in from no-route to any
block in from urpf-failed to any
block in quick on $ext_if from any to 255.255.255.255
block log all

pass in quick inet proto icmp icmp-type $icmp_types
pass in quick inet6 proto icmp6 

pass in quick proto tcp from <my_own_IPs> \
to (egress) port { 22 } \
flags S/SA modulate state \
(max-src-conn 5, max-src-conn-rate 5/5, overload <bruteforce> flush global)

pass in quick inet proto tcp from <target_whitelist> to port 3000
pass in quick inet6 proto tcp from <target_whitelist> to port 3000

block in quick log on egress all

pass out quick on egress proto tcp from any to any modulate state
pass out quick on egress proto udp from any to any keep state
pass out quick on egress proto icmp from any to any keep state
pass out quick on egress proto icmp6 from any to any keep state
```

After saving the configuration in /etc/pf.conf, reload the pf rules with:

```
pfctl -f /etc/pf.conf
```

12. Lastly launch MeshCentral so you can begin using it.
 
```
cd /usr/local/meshcentral/node_modules/meshcentral/ && doas -u _meshcentral /usr/local/bin/node /usr/local/meshcentral/node_modules/meshcentral/meshcentral.js --launch 
```

13. You can now access MeshCentral at https://your address:3000 or https://meshcentral.example.com:3000 if you named the machine meshcentral or create an A record named meshcentral. The first user you create will be the Administrator, there is no default user.
 
---

#### Notes:

* The “meshcentral-data” folder contains critical server information including private keys therefore, it’s important that it be well protected. It’s important to backup the "meshcentral-data” folder and keep the backup in a secure place. If, for example the “agent certificate” on the server is lost, there is no hope for agents ever be able to connect back to this server. All agents will need to be re-installed with a new trusted certificate.If someone re-installs a server, placing the “meshcentral-data” folder back with these certificates should allow the server to resume normal operations and accept connections for Intel AMT and agents as before.

---

#### Links:

* [User's Guide v0.2.2 - PDF document.](http://info.meshcentral.com/downloads/MeshCentral2/MeshCentral2UserGuide-0.2.2.pdf)
* [Install Guide v0.0.6 - PDF document.](http://info.meshcentral.com/downloads/MeshCentral2/MeshCentral2InstallGuide.pdf)
* [MeshCentral2 Design and Architecture](http://info.meshcentral.com/downloads/MeshCentral2/MeshCentral2DesignArchitecture.pdf)
