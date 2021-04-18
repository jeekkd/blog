+++
author = "Daulton"
title = "Create encrypted root on Gentoo"
date = "2018-08-28"
description = "This guide is for Gentoo Linux and we will be creating an encrypted root partition."
tags = [
    "linux",
    "guides",
]
categories = [
    "guides",
]
+++

This guide is for Gentoo Linux and we will be creating an encrypted root partition. We will be using a rather vanilla method without fancy gpg encrypted key files or anything of the sort. 
<!--more-->

This guide uses LUKS, if you would prefer plain dm-crypt [reference the following.](https://and1equals1.blogspot.com/2009/10/encrypting-your-hdd-with-plausible.html "https://and1equals1.blogspot.com/2009/10/encrypting-your-hdd-with-plausible.html")

#### Notices

* Wherever /dev/sda3 is mentioned, replace that with the partition you are making your encrypted root.
* I recommend you use  [SystemRescue CD](http://www.system-rescue-cd.org/SystemRescueCd_Homepage "http://www.system-rescue-cd.org/SystemRescueCd_Homepage")  to follow this guide.
   
----------

SystemRescue CD comes with Gparted, use it to create the following:

* A boot partition of ~2 gb or so, with the boot flag set
* Another partition equal to the size you want in total, this will have LVM to create other 'partitions' later and must be of adequate size for all your data.
    

----------

1. Load the dm-crypt kernel module

```
modprobe dm-crypt
```

2. Crypt the partition that you are making your root, in my case that would be /dev/sda3

```
cryptsetup -c aes-cbc-essiv:sha256 -v luksFormat -s 256 /dev/sda3
```

3. Open the luks volume

```
cryptsetup luksOpen /dev/sda3 sda3-luks
```

4. Setup a LVM physical volume

```
lvm pvcreate /dev/mapper/sda3-luks
```

5. Setup a volume group

```
lvm vgcreate vg1 /dev/mapper/sda3-luks
```

6. Create the logical volumes, make one of these for each 'partition' like you normally would. Before you go ahead and just create a root and swap partition, and nothing else, please consider reading the 'Configure your mount options securely' link at the bottom. This is a chance to configure things securely further

```
lvcreate --size 50G --name root vg1
lvcreate --size 10G --name swap vg1
```

7. Go ahead and review things, then finalize the new volume group by 'activating' it

```
pvdisplay
vgdisplay
lvdisplay
    
vgchange --available y
```

8. This should inform you that two LVs in the vg1 volume group have been activated. Check that they are visible by the device mapper:

```
ls -l /dev/mapper
```

9. Now we have our virtual partitions, we need to set up their filesystems and then mount them.

```
mkswap -L "swap" /dev/mapper/vg1-swap
swapon -v /dev/mapper/vg1-swap
```

10. Then format root as ext4

```
mkfs.ext4 -L "root" /dev/mapper/vg1-root
```

11. Next we will mount vg1-root on /mnt/gentoo, and then mount the boot partition at /mnt/gentoo/boot

```
mount -v -t ext4 /dev/mapper/vg1-root /mnt/gentoo
```

12. Don't forget to mount your /boot partition, this is a unencrypted, standard partition

```
mkdir /mnt/gentoo/boot
mount /dev/sdYX /mnt/gentoo/boot
```

13. This is where you would pick up from  [here in the Gentoo Handbook as an example](https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/Stage "https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/Stage"). After you progress through the handbook and complete it, you require some additional configuration to boot the encrypted disk. Continue back here after getting to this point.


14. Lets begin preparing the chroot

```
mount -t proc proc /mnt/gentoo/proc
mount --rbind /sys /mnt/gentoo/sys
mount --make-rslave /mnt/gentoo/sys
mount --rbind /dev /mnt/gentoo/dev
mount --make-rslave /mnt/gentoo/dev
chroot /mnt/gentoo /bin/bash && source /etc/profile
export PS1="(chroot) $PS1"
```

15. Genkernel is the recommended way to proceed here, although you could build your kernel and initramfs a different way if you desire

Emerge genkernel:

```
emerge --ask sys-kernel/genkernel-next
```

Compile the kernel and initramfs with lvm and luks support

```
genkernel --luks --lvm --busybox --menuconfig all
```


16. The GRUB configuration located at /etc/default/grub requires some extra information to how how to boot our set-up, it needs to know the UUID of the /dev/sda3 encrypted partition

Before we can do that, assure you have a bootloader installed. You could use LiLo here too, but I will show you this with GRUB2.

```
emerge --ask sys-boot/grub:2 sys-boot/os-prober
```

17. Now edit /etc/default/grub with your text editor of choice

```
GRUB_CMDLINE_LINUX="crypt_root=UUID=<uuid of sda3> dolvm"
```

After setting the above proceed to install GRUB to /dev/sda and then generate the configuration

```
grub-install /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg
```

**Note:**  Assure that the config isn't named  _/boot/grub/grub.cfg.new_before you reboot, it does this sometimes for whatever reason

----------

Complete! Now you can follow the next steps to continue from this point

----------

Next steps:

-   [Mount the logical volumes in your /etc/fstab](https://wiki.gentoo.org/wiki/Sakaki%27s_EFI_Install_Guide/Final_Preparations_and_Reboot_into_EFI#Setting_up_the_Mountpoint_Tables "https://wiki.gentoo.org/wiki/Sakaki%27s_EFI_Install_Guide/Final_Preparations_and_Reboot_into_EFI#Setting_up_the_Mountpoint_Tables")
    
-   [Configure your mount options securely](https://wiki.gentoo.org/wiki/Security_Handbook/Mounting_partitions "https://wiki.gentoo.org/wiki/Security_Handbook/Mounting_partitions")

