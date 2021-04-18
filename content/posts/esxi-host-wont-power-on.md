+++
author = "Daulton"
title = "esxi guest wont power on"
date = "2018-08-28"
description = "Fixing the Vmware ESXi guest won't power on, in current state (powerd off) error"
tags = [
    "guides",
]
categories = [
    "guides",
]
+++


### Fixing the Vmware ESXi host won't power on, in current state (powerd off) error
<!--more-->

After I rebooted the vm, changed some settings (attached a dvd drive), it would boot up again. I have had the same happen in a strange instance from removing an ISO from the dvd drive while the VM was on, then rebooting it.

If you receive this error notice:

> Failed - The attempted operation cannot be performed in the current state (Powered off).

Then do the following:

Right click the effected virtual machine, select unregister, then confirm by clicking 'Yes'.

To re-register it:

1. Click 'Create / Register VM'
    
2. Select 'Select one or more virtual machines, a datastore or a directory'
    
3. Navigate to where the .vmx for the VM in question is, select it, then click 'Next' and and then 'Finish'.
    
The VM should now boot normally again.

