+++
author = "Daulton"
title = "Linux: Creating an encrypted home partition with dm-crypt"
date = "2018-08-28"
description = "Creating an encrypted home partition with dm-crypt on Linux"
tags = [
    "linux",
    "guides",
]
categories = [
    "guides",
]
+++

Creating an encrypted home partition is a great way to protect your personal files and data, it makes it unreadable to those without access to your passphrase to open the partition at boot. If they do not have your passphrase (or you forget it!) then home will not be unlocked and renders the data unreadable and unrecoverable as it is AES encrypted, this is wonderful for those with laptops and personal files they don't wish to risk the chance of losing.
<!--more-->

#### Recommendations:

* I recommend that you boot into  [SystemRescue CD](http://www.system-rescue-cd.org/SystemRescueCd_Homepage "http://www.system-rescue-cd.org/SystemRescueCd_Homepage")  to complete this guide. You cannot be using the hard drive as you encrypt it.
* Take your time reading.
    

#### This guide is Linux centric and will require the following utilities:

* cryptsetup
    
#### Remember:

* Through out the guide replace /dev/sda5 with the correct drive and partition number representing your home partitions correct path.
* Using dm-crypt to encrypt your home partition requires it to be on a separate partition then / (root) is on. So I recommend firing up gparted and creating a new ext4 partition the desired size. Gparted is included in SystemRescue CD.  

#### Onto the guide now,

1. Mount the partition that your root is on. Open gparted and verify this if you are not certain.
    
```
mkdir /mnt/mychroot
mount /dev/sdYn /mnt/mychroot
```

2. Mount the following directories into your root partition to emulate a real system for when we go into the chroot environment
    
```
mount -o rbind /dev /mnt/mychroot/dev
mount -t proc none /mnt/mychroot/proc
mount -o bind /sys /mnt/mychroot/sys
mount -o bind /tmp /mnt/mychroot/tmp
```

3. We will now enter the chroot so we can get started.
    
```
chroot /mnt/mychroot /bin/bash
export PS1="(chroot) $PS1"
```

For Gentoo Linux you will additionally need to do the following after entering the chroot

```
env-update && source /etc/profile
```

4. The first thing that should be done is backup your home partition as we will be deleting /home and moving files around.
    
```
mkdir /home_backup
cp -ar /home/ /home_backup
ls -al /home_backup
rm -rf /home
```

5. The partition will next be filled with random data to erase what was previously there so no junk files can be recovered if it is analyzed.
  
```  
dd if=/dev/urandom of=/dev/sda5 bs=8192
```

or

```
shred -vfz -n 2 /dev/sda5
```

Note: This will take a while depending upon the size of the partition. No progress is shown, so don't worry it is not frozen. Alternatively you can use  [shred](http://linux.die.net/man/1/shred "http://linux.die.net/man/1/shred")  or  [hdarms secure erase](http://man7.org/linux/man-pages/man8/hdparm.8.html#ATA_Security_Feature_Set). Some solutions destroy the partition so you may need to recreate the partition afterward before continuing to the next step.



6. Next we are will create the encrypted partition and set the passphrase. Assure you are setting  [a very strong yet memorable passphrase](http://www.iusmentis.com/security/passphrasefaq/ "http://www.iusmentis.com/security/passphrasefaq/")  ([or here](https://wiki.archlinux.org/index.php/Security#Passwords "https://wiki.archlinux.org/index.php/Security#Passwords")) as this is the gateway to your data.

```  
cryptsetup -c aes-xts-plain -s 512 -v -y luksFormat /dev/sda5
```


7. We will now open the encrypted partition and be formatting it as ext4.
    
```
cryptsetup luksOpen /dev/sda5 home
mkfs.ext4 /dev/mapper/home
```

​Dm-crypt uses mapped partitions, by specifying home we are labeling that mapped drive. You can do the following to see your mapped drive exists and where is resides

```
ls /dev/mapper/
```


8. Next we must modify the dmcrypt configuration file to tell it which drive is encrypted so it will prompt to decrypt that drive at boot time.
    
```
vi /etc/conf.d/dmcrypt
```

You will want to scroll down to the section that says 'dm-crypt examples'. There is a ready made example that we can modify for our usage that is called '/home with passphrase'. Uncomment it and fill your information in.

```
## /home with passphrase
target=home
source='/dev/sda2'
```

9. Your target may be different if your changed the label in the above cryptsetup command. If you did not then yours will be the same. Substitute your partition in.

For Gentoo Linux the following is required so dmcrypt starts at boot and is there to handle the decryption of the partition
    
```
rc-update add dmcrypt boot
```


10. The fstab must be edited so the system knows what drive to mount to where. Do the following to get the UUID of the partition you are using for your encypted home.
    
```
blkid
```

Now open it

```
vi /etc/fstab
```

You will want to have something such as the following but assure to replace the '????' with the UUID of the partition you gained with the above command.

```
UUID=???? /home ext4 defaults,noatime 0 2
```

11. We will now mount the partition as it is already open, and we will put the backed-up home directory inside of it.
    
```
mkdir /home
mount -v /dev/mapper/home /home
cp -ar /home_backup/home/* /home/
```

12. Assure that the ownership and permissions are proper for the ownership of the home directory itself, the user folders within, and the file permissions for all files within.
    
```
chown root:root /home
chmod -R 755 /home/
chown <your user>:<your user> /home/<your user>
```

Afterward you may check with ls -l to make sure the settings are set favourably.

----------

#### Complete.

Now you should be successfully done, reboot and verify. You should be presented with a prompt to unlock your encrypted partition with the passphrase selected earlier. Enjoy.


### Further Reading:

1. [Extra things on attacks, strong passphrase, etc](http://gentoo-en.vfose.ru/wiki/DM-Crypt_with_LUKS#Further_reading "http://gentoo-en.vfose.ru/wiki/DM-Crypt_with_LUKS#Further_reading")   
2. [FAQ](http://gentoo-en.vfose.ru/wiki/DM-Crypt_with_LUKS#FAQ "http://gentoo-en.vfose.ru/wiki/DM-Crypt_with_LUKS#FAQ")
3. [Ciphers](http://gentoo-en.vfose.ru/wiki/DM-Crypt_with_LUKS#Ciphers "http://gentoo-en.vfose.ru/wiki/DM-Crypt_with_LUKS#Ciphers")
