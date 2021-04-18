+++
author = "Daulton"
title = "Configuring Minicom on Linux"
date = "2018-08-28"
description = "This is a guide of how to configure and use Minicom"
tags = [
    "linux",
    "guides",
]
categories = [
    "guides",
]
+++

This is a guide of how to configure and use Minicom. Minicom is a [text-based](https://en.wikipedia.org/wiki/Text-based "https://en.wikipedia.org/wiki/Text-based")  [modem](https://en.wikipedia.org/wiki/Modem "https://en.wikipedia.org/wiki/Modem")  control and  [terminal emulation](https://en.wikipedia.org/wiki/Terminal_emulation "https://en.wikipedia.org/wiki/Terminal_emulation")  program for  [Unix-like](https://en.wikipedia.org/wiki/Unix-like "https://en.wikipedia.org/wiki/Unix-like")  operating systems, this is used for such applications such as connecting to Cisco networking equipment or other equipment that can take serial input as a means to configure these sorts of devices.
<!--more-->

----------

1. Install Minicom for your system. Below are some examples of how you'd do so for a couple different systems

Debian based:

```
apt install minicom
```

Gentoo:

```
emerge --ask net-dialup/minicom
```

Fedora:

```
dnf install minicom
```

CentOS/RHEL:

```
yum install minicom
```

2. Plug your USB serial adapter in and issue the following command. We need to determine which tty your device gets mapped to

```
dmesg
```

You should see something similar to the following occur. **Note the last line how udev creates a device in /dev and assigns this USB device to it.**  This is what you input into programs such as Minicom to use the device

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

Note the last line the device that it says it is mapped to, for me it is ttyUSB0

3. Now we will configure Minicom to use this device

```
minicom -s
```

You will now get a menu like so:

![Minicom menus](/images/minicom-guide/main_menu1.png)


4. Now go to 'Serial port setup' and match your options to mine with the exception of substituting your device in with the one we determined in step two.

![Minicom menus](/images/minicom-guide/main_menu2.png)


5. Select 'Save setup as dfl'

![Minicom menus](/images/minicom-guide/main_menu3.png)


6. Go to 'Save setup as..' and type a name for this configuration. This will be the name you will type when you want to use this configuration so make sure it is something memorable

![Minicom menus](/images/minicom-guide/main_menu4.png)


7. Now we can go ahead and exit since the configuration is all done for this device. We can now get onto testing and using it

![Minicom menus](/images/minicom-guide/main_menu5.png)


8. The following is what you will need to do to use your new configuration

```
sudo minicom <setup name you entered earlier>
```

As an example I named mine cisco so I would type this:

```
sudo minicom cisco
```

----------

**Complete!**  You now have Minicom installed and configured to use your USB serial device as well as now know how to launch your configuration.


