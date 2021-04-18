+++
author = "Daulton"
title = "Pfsense: Port forward traffic to a specific host"
date = "2018-08-28"
description = ""
tags = [
    "pfsense",
    "guides",
]
categories = [
    "guides",
]
+++

![pfsense logo](/images/pfsense/pfsense-logo.jpg)

This guide will be based around PfSense version 2.3.x. It will shown how to Port Forward to a specific host to enable the flow of traffic aimed at your network that is supposed to be forwarded to a specific host or a load balancer in front of many other hosts.
<!--more-->

Before continuing I recommend reading  [How can I forward ports with pfSense](https://doc.pfsense.org/index.php/How_can_I_forward_ports_with_pfSense "https://doc.pfsense.org/index.php/How_can_I_forward_ports_with_pfSense")  and  [Port Forward Troubleshooting](https://doc.pfsense.org/index.php/Port_Forward_Troubleshooting "https://doc.pfsense.org/index.php/Port_Forward_Troubleshooting")

----------

#### What is Port Forwarding?

> In computer networking, port forwarding or port mapping is an application of network address translation (NAT) that redirects a communication request from one address and port number combination to another while the packets are traversing a network gateway, such as a router or firewall. This technique is most commonly used to make services on a host residing on a protected or masqueraded (internal) network available to hosts on the opposite side of the gateway (external network), by remapping the destination IP address and port number of the communication to an internal host.
> 
> [Reference](https://en.wikipedia.org/wiki/Port_forwarding "https://en.wikipedia.org/wiki/Port_forwarding")

----------

#### Navigate to on the top bar a main section called Firewall, next click NAT, and finally go to Port Forward.

![pfsense-port-forwarding-1](/images/pfsense/pfsense-port-forwarding-1.png)

Before proceeding there is some information you require, you must find out what sort of traffic it is, which protocol, which IP the host is, and so forth. This is required to create the rule so the correct traffic is forwarded.

In the above instance you can see there are two sets of rules for forwarding BitTorrent traffic on two different ranges of ports to a specific host.

----------

#### Go to the green add rule button to begin adding a new rule

Using the information you have gathered you can then use it fill out the page. We will continue on under the following scenario, that we have a computer located at 192.168.1.50 we wish to have traffic that for port 6667 is forwarded to. As an  IRC  server it uses connections with TCP, knowing this is necessary. You can find this out by searching something like  _'<software name> <port number> <tcp or udp?>_'

We will also add a  _Filter rule association_  which is set to  _Add associated filter rule_  for the purpose of having the firewall rules set automatically and then have them associated to the Port Forward. This way when you update the Port Forward the firewall rules are updated as well.

----------

#### Fill everything out

![pfsense-port-forwarding-2](/images/pfsense/pfsense-port-forwarding-2.png)

Note: Your destination IP will likely be set to  _WAN Address_ but if your router is behind another you may need to use private addresses instead such as how I am.

> Destination: Specifies the original destination IP address of the traffic, as seen before being translated, and will usually be WAN address.

After you have filled everything out it should look similar to this, plug in all the information. To explain it simply what is being done is:

> To explain how this screen's functionality translates into English: Take traffic entering the chosen interface, using the specified protocol, initiated from the specified source, destined to the specified destination, and redirect it to the specified target IP and port.
> 
> [Reference](https://doc.pfsense.org/index.php/How_can_I_forward_ports_with_pfSense "https://doc.pfsense.org/index.php/How_can_I_forward_ports_with_pfSense")

If you do the above, and then assure that on the server side that you have allowed that traffic through the firewall as well so connections can be initiated. Below are some examples of allowing port 6667 through the firewall of the box that will have traffic forwarded to it:

iptables:

```
iptables -A INPUT -p tcp -m state --state NEW,ESTABLISHED,RELATED --dport 6667 -j ACCEPT
```

ufw:

```
ufw allow 6667/tcp
```

firewalld:

```
firewall-cmd --permanent --zone=public --add-port=6667/tcp
```

pf:

```
pass in on egress proto tcp from any to any port 6667
```

After editing /etc/pf.conf, reload the rules with pfctl -f /etc/pf.conf.

Windows:

```
netsh advfirewall firewall add rule name="Open Port 6667" dir=in action=allow protocol=TCP localport=6667
```

After this you should now be able to test to see if this all works.

----------

#### Troubleshooting

* [Why can't I access forwarded ports on my WAN IP from my LAN/OPTx networks](https://doc.pfsense.org/index.php/Why_can%27t_I_access_forwarded_ports_on_my_WAN_IP_from_my_LAN/OPTx_networks "https://doc.pfsense.org/index.php/Why_can%27t_I_access_forwarded_ports_on_my_WAN_IP_from_my_LAN/OPTx_networks")
    
* [Port Forward Troubleshooting](https://doc.pfsense.org/index.php/Port_Forward_Troubleshooting "https://doc.pfsense.org/index.php/Port_Forward_Troubleshooting")
    
* [Connectivity Troubleshooting](https://doc.pfsense.org/index.php/Connectivity_Troubleshooting "https://doc.pfsense.org/index.php/Connectivity_Troubleshooting")

