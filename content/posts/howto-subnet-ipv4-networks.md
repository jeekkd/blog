+++
author = "Daulton"
title = "How-to subnet ipv4 networks"
date = "2018-08-28"
description = "How-to subnet ipv4 networks"
tags = [
    "guides",
    "networking",
]
categories = [
    "guides",
]
+++

Lets start with the definition of subnetting to get a clear idea of what it is we are doing exactly
<!--more-->

A **subnetwork**, or **subnet**, is a logical, visible subdivision of an IP network. The practice of dividing a network into two or more networks is called  **subnetting**.  

With that established, what we are doing is the practice of dividing a network into multiple smaller networks. This is for a variety of reasons, some logical some technical. Logical reasons include having a different department or guest network on a different subnet to segmentation purposes, this can add security. Technical reasons include limiting the scope of broadcasts.

----------

Before you arbitrarily pick a network address to start splitting up there are some basic things to understand first. You may have heard of network classes before, with your common 192.168.y.z being a class C network with the usual 255.255.255.0 netmask. For private networks that would be in a home or office you must pick a network address that falls within one the ranges for Private IPv4 address spaces (defined in  [RFC1918](https://tools.ietf.org/html/rfc1918 "https://tools.ietf.org/html/rfc1918"))

| Network Range | largest CIDR block |
|--|--|
| 10.0.0.0 - 10.255.255.255 | 10.0.0.0/8 |
| 172.16.0.0 - 172.31.255.255 | 172.16.0.0/12 |
| 192.168.0.0 - 192.168.255.255 | 192.168.0.0/16 |

So you could pick 172.20.x.y as an example.

----------

To divide a network you first need a network to split up. First you must take into consideration how many hosts your entire network must support, let's say you need to support in the area of ~500 devices spread across various subnets for various purposes. This comes awfully close to the upper amounts of what a /23 for the amount of hosts, a /22 network would be ideal as it would have room for future growth.

Now what am I meaning by /22? It is called CIDR notation as explained best below:

CIDR notation is a compact representation of an IP address and its associated routing prefix. The notation is constructed from an IP address, a slash ('/') character, and a decimal number. The number is the count of leading  _1_bits in the routing mask, traditionally called the network mask.

This is significant because both the CIDR notation or netmask are able to tell you a lot about the subnet size, ranges, broadcast and network address, etc. Since the CIDR notation is representative of how many bits are active it can be shown like so:

> 111111111.111111111.111111100.00000000

What does this tell you? First you need to understand binary and binary conversions. Each octet which an ipv4 address has four of, has eight bits each totaling 32 bits for the entire address. Each octet has eight bits that can either be on or off, with 1 being on.

| 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 |
|--|--|--|--|--|--|--|--|

At each position within an octet the following values represent what the value at each position can be. In the case of the given /22 netmask that was expanded above it can be converted from binary like so by adding the values up:

```
111111111 = 255
111111111 = 255
111111100 = 252
00000000 = 0
```

This is because:

| 1st | 128 + 64 + 32 + 16 + 8 + 4 + 2 + 1 | equals 255 |
|--|--|--|
| 2nd | 128 + 64 + 32 + 16 + 8 + 4 + 2 + 1 | equals 255 |
| 3rd | 128 + 64 + 32 + 16 + 8 + 4 | equals 252 |
| 4th | no bits active | equals 0 |

----------

By looking at and understanding the subnet mask in binary you can begin to understand a lot. Active bits (1) represent the network portion, with inactive (0) bits being the host portion.

111111111.111111111.111111100.00000000

With this subnet mask we have 22 network and 10 host bits. If 8 bits in an octet can add up to 255 (technically 256 since counting starts at 0), how much does 10 bits add up to? Each bit doubles its value as you move further to the left, it will next go to 512 (9 bits) and then 1024 (10 bits).

This means the host portion of the network can contain up to 1024 hosts, and 1022 assignable the reason for having less assignable addresses is explained in a moment.

----------

Lets start with a 172.20.x.y address. This starts off as 172.20.x.y /22 being a single, large network containing all 1024 hosts, there is no subnetting done to it yet. With every network and subnet there are two nonassignable addresses, the network address and the broadcast address.

Network addresses are an identifier to your network, it is significant to routers for routing traffic to and from your network. A network address looks much like a regular address in some cases but it has a different meaning. That difference comes from where the network or subnet begins, the very first address is nonassignable and reserved for the network address.

The broadcast address is the very last address of the network or subnet that is outside the assignable range. Broadcast addresses are necessary within a network for broadcast functions required by certain protocols such as DHCP or ARP.

Going forward from here:

| Network address | 172.20.0.0 |
|--|--|
|Broadcast address  | 172.20.3.255 |
|Subnet address| 255.255.252.0 |
|Assignable range | 172.20.0.1 - 172.20.3.254 |

----------

### Practice

* [subnettingpractice.com](http://subnettingpractice.com/index.html "http://subnettingpractice.com/index.html")

