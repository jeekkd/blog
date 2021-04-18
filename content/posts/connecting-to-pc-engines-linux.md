+++
author = "Daulton"
title = "Linux: Connecting to PC Engines APU board "
date = "2018-08-28"
description = "How to connect to PC Engines APU board with Minicom."
tags = [
    "linux",
    "guides",
]
categories = [
    "guides",
]
+++

I will be walking you through connecting to your [PC Engines APU board](http://www.pcengines.ch/apu.htm "http://www.pcengines.ch/apu.htm")  on Linux. This guide will use  [Minicom](http://linux.die.net/man/1/minicom "http://linux.die.net/man/1/minicom")  as our means to utilize the serial connection to the board and communicate with it.
<!--more-->

----------

- First, determine your device is recognized
    
```
dmesg
```

- You should see output such as the following
    

```
[  989.047171] usb 3-1: new full-speed USB device number 2 using xhci_hcd
[  989.217071] usb 3-1: New USB device found, idVendor=0403, idProduct=6001
[  989.217075] usb 3-1: New USB device strings: Mfr=1, Product=2, SerialNumber=3
[  989.217077] usb 3-1: Product: FT232R USB UART
[  989.217079] usb 3-1: Manufacturer: FTDI
[  989.217080] usb 3-1: SerialNumber: AE017J6J
[  989.249595] usbcore: registered new interface driver ftdi_sio
[  989.249648] usbserial: USB Serial support registered for FTDI USB Serial Device
[  989.249875] ftdi_sio 3-1:1.0: FTDI USB Serial Device converter detected
[  989.249935] usb 3-1: Detected FT232RL
[  989.250173] usb 3-1: FTDI USB Serial Device converter now attached to ttyUSB0
```

Note: your output will defer slightly as your device is likely different. If you do not see output such as the above and a device created, determine the driver module your USB serial adapter requires and attempt to do 'modprobe <module name>'. Try plugging the adapter in again, and issuing another 'dmesg' command to see if it is recognized and a device gets created.

Example:

```
modprobe cp210x
dmesg
```

- Follow the  [installing and configuring Minicom guide](configuring-minicom-on-linux.html)
    
- Use the following settings during the configuration stage, which is step #4 in the above guide
    

![minicom settings](/images/pcengines-apu/pfsense_minicom.png)

**The settings you need are the following:**  115200 8N1

----------

This will be the correct speed and bit settings to give you a clear and fast enough terminal session. This is also the default for  [PfSense](https://www.pfsense.org/ "https://www.pfsense.org/"), which is popular to install on these boards.

