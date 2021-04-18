+++
author = "Daulton"
title = "pfSense route specific hosts over VPN"
date = "2018-10-07"
description = "The following guide will show you how to route specific LAN hosts network traffic over an existing VPN setup in your pfSense installation."
tags = [
    "pfsense",
    "guides",
]
categories = [
    "guides",
]
+++

The following guide will show you how to route specific LAN hosts network traffic over an existing VPN setup in your pfSense installation.
<!--more-->

----------

## Alias setup

Aliases ease firewall rule setup and keep your rules clean by reducing the amount of rules needed. An alias can be made to reference multiple sets of ports or IPs.

* Go to Firewall > Aliases and press the 'Add' button.
* Give it a name such as VPNhosts
* Then in the Host(s) section add the IP or FQDN for each host. You can add multiple entries, make sure to add all the hosts that should be routed over your VPN.
* Once done press 'Save'.

----------

## Rule setup

Next is the creation of the rule to direct traffic from the hosts listed in the alias into a specific gateway. By doing so we make traffic from your LAN hosts always go over the VPN.

* Go to Firewall > Rules > LAN
* Add a new rule at the top of your ruleset, anywhere before your pass all rule
* Edit the rule to reflect the following:
  * Action: Pass
  * Address family: ipv4
  * Protocol: tcp/udp
  * Source: Single host or Alias - then type your Alias name: ex VPNhosts
  * Destination: Any
  * Description: Route VPNhosts alias hosts to VPN network
  * Expand the advanced options then find Gateway. Select the gateway of your VPN, it may be called VPN_VPN4 or something similar.
* Press 'Save'.

After creating the above rule, traffic from the hosts you added to the alias will now be routed over the VPN.
