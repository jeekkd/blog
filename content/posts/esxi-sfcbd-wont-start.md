+++
author = "Daulton"
title = "ESXi 6.5 Service sfcbd-watchdog Not Running"
date = "2018-09-04"
description = "Solution to the sfcbd-watchdog service not running on ESXi 6.5"
tags = [
    "guides",
]
categories = [
    "guides",
]
+++

### Solution to the sfcbd-watchdog service not running on ESXi 6.5
<!--more-->

**Note:** *All credits to the original author for this information. [Please reference his page here](http://vcdx56.com/2017/06/esxi-6-5-service-sfcbd-watchdog-not-running/), I am providing a mirror for this valuable information.*

A few days back I had problem with the VMware ESXi service sfcbd-watchdog which wouldn't start on my newly deployed ESX 6.5a (build number 4887370) servers and since the service is used for hardware monitoring purposes it is quite important. The service is disabled and is in stopped state from ESXi 6.5 so this blog post will be about my findings and how you can get the service to a running state.

There is an option to control all services via the ESXi host client which you can access via https://ESXi-host-FQDN. After logging on to the ESXi host client as user root you go to Host -> Manage -> Services
As you can see in the below figure the service is in Stopped state.

![vmware-service-sfcbd-00.png](/images/vmware/vmware-service-sfcbd-00.png)

After I tried to start the service by clicking Start button you can see in the below figure that the UI shows the service as Running but I still couldn't connect to the service.

![vmware-service-sfcbd-01.png](/images/vmware/vmware-service-sfcbd-01.png)

I checked the service status from command line by running the following command:

```
/etc/init.d/sfcbd-watchdog status
```

Gave me the following output:

> sfcbd is not running

Since status was not running I tried to start the service by running the following command:
Trying to start the sfcbd-watchdog service manually via the command:

```
/etc/init.d/sfcbd-watchdog start
```

The start command gave me the following output:

```
sfcbd-init: Getting Exclusive access, please wait...
sfcbd-init: Exclusive access granted.
sfcbd-init: Request to start sfcbd-watchdog, pid 68162
sfcbd-config[68172]: No third party cim providers installed
sfcbd-init: sfcbd not started, administratively disabled.
```

The output made me check the system wbem status running the following command:

```
esxcli system wbem get
```

The output I got was:

> Authorization Model: password
> Enabled: false
> Loglevel: warning
> Port: 5989
> WSManagement Service: true

Since it returned "Enabled=false" I enabled it by running the following command:

```
esxcli system wbem set --enable true` 
```

Note: two minus signs before enable

One again checking the wbem status by running the wbem status command:

```
esxcli system wbem get
```

Game me the following output:

> Authorization Model: password
> Enabled: true
> Loglevel: warning
> Port: 5989
> WSManagement Service: true

After that I checked the service status by running the sfcbd-watchdog status command:

```
/etc/init.d/sfcbd-watchdog status
```

Gave me the following output:

> sfcbd is running

All good and service is up and running.

