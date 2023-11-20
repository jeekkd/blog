+++
author = "Daulton"
title = "Install PiKVM on Debian 12 x86"
date = "2023-11-20"
lastmod = "2023-11-20"
description = "A guide to help install PiKVM on Debian 12 x86"
tags = [
    "homelab",
    "linux",
]
categories = [
    "guides",
]
+++

Recently I came across [PiKVM](https://pikvm.org/) and was very interested in having a low-cost IP KVM in my homelab, <!--more--> however with the current availability issues of the Raspberry Pi and the high cost of other single board computers, I needed another solution. After more searching I found that you can [install PiKVM on x86](https://github.com/srepac/kvmd-armbian)! This is great. However, there was one thing I would like to adjust - Ubuntu. I prefer not to use Ubuntu and to instead stick to Debian where possible. So the following guide offers purchasing tips for hardware that worked for me, and also what to do to get PiKVM running on [Debian](https://www.debian.org/) 12.

 
I bought the following hardware and I can confirm it works for me. I modeled this as closely as I could from [the install PDF in the repo](https://github.com/srepac/kvmd-armbian/blob/master/How%20to%20Install%20PiKVM%20x86.pdf), this was because on Canadian Amazon.ca I either could not find the parts and/or the price was ridiculous.

To successfully install PiKVM on your own x86 hardware, you will be using my guide for where the installation step differs but for the most part follow the steps in the "[How to Install PiKVM x86.pdf](https://github.com/srepac/kvmd-armbian/blob/master/How%20to%20Install%20PiKVM%20x86.pdf)" document. There are two additional packages to install, and changes to step 3 and 5 - see below for what to do.
  
## Products I purchased

 - [PL2303 TA USB TTL RS232 Convert Serial Cable](https://www.aliexpress.com/item/1005004938348259.html)
 - [HDMI-compatble to USB 2.0 Video Capture Card Local LOOP](https://www.aliexpress.com/item/1005005316223739.html)
 - [CH9329 UART TTL Serial Port To USB HID Full Keyboard And Mouse Module](https://www.aliexpress.com/item/1005005802594247.html)

## Some tips on connections, 

#### HDMI capture card

 - Plug the male micro USB into the HDMI capture card and the USB-A male end into your PiKVM box.
 - Plug the USB-A male end of the other USB cable into the HDMI capture card and the other end into your PiKVM box.
 - Plug a HDMI cable into the system you wish to control and plug the other end into the HDMI input on the capture card.
 
 #### USB HID
- The USB-A end of the PL2303 USB cable goes into the PiKVM box, and the serial cable end of the cable gets plugged into the 
- The UART TTL Serial Port To USB HID goes into the system you wish to remotely manage.

## Changes to the install process

 - Before running the install script, install the following packages.

    apt install python3-async-lru sudo

- Next following the installation doc in step 3 run the installer again for part two of the install script. The install.sh part 2 will fail with the below error. 

       -> Checking kvmd -m works before continuing
        Traceback (most recent call last):
        File "/usr/bin/kvmd", line 5, in <module>
        from kvmd.apps.kvmd import main
        File "/usr/lib/python3/dist-packages/kvmd/apps/kvmd/__init__.py", line 38, in <module>
        from .server import KvmdServer
        File "/usr/lib/python3/dist-packages/kvmd/apps/kvmd/server.py", line 84, in <module>
        from .api.export import ExportApi
        File "/usr/lib/python3/dist-packages/kvmd/apps/kvmd/api/export.py", line 44, in <module>
        class ExportApi:
        File "/usr/lib/python3/dist-packages/kvmd/apps/kvmd/api/export.py", line 56, in ExportApi
        @async_lru.alru_cache(maxsize=1, ttl=5)
        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
        TypeError: alru_cache() got an unexpected keyword argument 'ttl'

  
  At this point open another terminal, edit the below file on line 56,


      /usr/lib/python3/dist-packages/kvmd/apps/kvmd/api/export.py

  
  Change this line:

      @async_lru.alru_cache(maxsize=1, ttl=5)

  Remove the parameters in the parenthesis so it is like so:

      @async_lru.alru_cache()

  
  Then after the file edit is completed answer 'n' to the prompt 'Did kvmd -m run properly?' to get it to re-check. After the file edit, this time it will succeed.


- The last edit to the installation process is in step 5 for the udev rules. Edit the following path instead of the one suggested in the install doc.

      /etc/udev/rules.d/99-kvmd.rules

 
