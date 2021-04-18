+++
author = "Daulton"
title = "My Shell Scripts"
date = "2018-08-27"
description = "My Shell Scripts"
tags = [
    "scripting",
]
categories = [
    "portfolio",
]
+++

![bash logo](/images/bash-script-screenshots/bash-logo-web.png)

I enjoy writing Bourne Again (Bash) and Bourne Shell (Sh) scripts, I've got a ton of them that do various things like backup my important files, system backup and restore, automate the usage of kvm-qemu, system updates, mount encrypted volumes, etc. They can be tremendously useful and even save you time as well as serving as a form of documentation to the process.
<!--more-->

A massive reason to love shell scripts is portability, most if not  _nearly_all Linux systems have Bash or Bourne Shell. So when you write a good quality script it will run across many different systems, even on older versions of the shell with some exceptions. If one were to want to learn how to script with BASH  [here are some resources](https://daulton.ca/doku.php?id=wiki:bash_guide "wiki:bash_guide")  I have compiled that would help with doing so.

----------

#### Here are some of my scripts:

* [gentoo-kernel-build](https://github.com/jeekkd/gentoo-kernel-build "https://github.com/jeekkd/gentoo-kernel-build")
    

Automating the kernel emerge, eselect, compile, configuration copying and detection, hardware detection install, etc along with grub configuration updating to save some effort when installing, upgrading, or trying a new kernel.

**Features**

* A selection of kernels to choose from including gentoo-sources, hardened-sources, vanilla-sources, pf-sources, etc  
* The option to choose to unmask testing versions of your selected kernel
* Handles the eselect kernel set by giving you a list of installed kernels to select in a easy manner 
* If dependencies are not installed you are prompted if you would like the script to install them
* The option to choose to copy a kernel .config from the scripts running directory to the selected kernels location or using the kernel from the running system
* Makes use of kergen for enabling hardware support for the currently running system in the selected kernel
* Options of compiling the kernel with the regular make method, Sakakis build kernel script, or Genkernel
* In all compiling methods you are given the amount of CPU cores available and prompted to select how many to use
* In the regular make method and Genkernel selections you are given the option of using menuconfig or gconfig for kernel customization
* Supports updating your GRUB config, or if GRUB is not installed it will be installed and walk you through configuration by prompting which disk to install to, etc.  
* Among other nifty features..
    

**Pictures**

![build-kernel-01](/images/bash-script-screenshots/build-kernel-01.png)

![build-kernel-02](/images/bash-script-screenshots/build-kernel-02.png)

![build-kernel-03](/images/bash-script-screenshots/build-kernel-03.png)

----------

* [kvm-qemu-menu](https://github.com/jeekkd/kvm-qemu-menu "https://github.com/jeekkd/kvm-qemu-menu")
    

The purpose of this script is to make basic use of kvm-qemu easier for personal usage. It doesn't try to do everything, it does however do a broad amount of things that are necessary for basic creation and usage of virtual machines.

Some features include; Virtual disk and machine creation, run virtual machines menu that additionally launches the network script at run-time, disk resizing, snapshots both the creation and changing of them as well as deletion, reinstall operating system, etc.

For more detailed information please refer to the README on the gitlab page.

![kvm script](/images/bash-script-screenshots/kvm.png)

----------

* [backup-gentoo](https://github.com/jeekkd/backup-gentoo "https://github.com/jeekkd/backup-gentoo")
    
The purpose of this script is to make the backup and restoration of Gentoo systems easier. This is achieved by having a convenient menu, both backup and restore capable, and minimal interaction aside from setting a few variables before being ran.

This script could be adapted to be used on other distributions if one were to edit the chroot_commands.sh script to reflect the commands that need to be ran for your distribution to install and configure GRUB. That would fairly easy, so if you are not using Gentoo but need a script like this give it a go.

![backup script](/images/bash-script-screenshots/backup.png)

----------

* [mount-truecrypt](https://github.com/jeekkd/mount-truecrypt "https://github.com/jeekkd/mount-truecrypt")
    

The purpose of this script is to make mounting truecrypt containers simpler and easier.

While there is TrueCrypt and VeraCrypt  GUI  programs that will fulfill this, those who prefer to maintain a light system may prefer a script instead or merely prefer a simple solution to a simple job.

![truescript script](/images/bash-script-screenshots/truecrypt.png)

----------

* [rsync-truecrypt](https://github.com/jeekkd/rsync-truecrypt "https://github.com/jeekkd/rsync-truecrypt")
    

The purpose of this script is to open and mount the truecrypt container and then go about its job syncing all the desired files and directories to their selected destinations. This script handles files and directories from anywhere to anywhere, so you can choose many things to backup to specific locations within your container to maintain organization.

This makes backups very quick and easy after you get your paths in the variables section set. What is great about rsync is by default does not copy a file if it exists without having been changed, by default this behavior is based on time stamps and file size, so this means you can run this script and only what has changed will be copied.

![rsync truescript script](/images/bash-script-screenshots/rsync_truecrypt.png)

----------

* [cleanup-gentoo](https://github.com/jeekkd/cleanup-gentoo "https://github.com/jeekkd/cleanup-gentoo")
    
The purpose of this script is to just be a simple script to run after uninstalling packages to clean the dependencies or wanting to clean portages leftovers from previous emerges. There is a couple extra handy things like emptying the trash, cleaning /var/tmp and /tmp, checks the system for compliance with Gentoo Linux Security Advisories, empty browser cache, etc.

----------

* [restricted-iptables](https://github.com/jeekkd/restricted-iptables "https://github.com/jeekkd/restricted-iptables")
    
This is a configurable iptables firewall script, meant to make firewalls easier.

All sorts of knobs are available in configuration.sh for enabling or disabling various parts of the script. If you are curious what is available I suggest taking a look in configuration.sh to see, there is a fair amount you can do.

> Note: This script is originally intended to be a restricted firewall, operating in a default deny sort of manner where it is very locked down. Since the beginning it has been expanded, allowing default policies and variables changed as the user desires through the options given. This allows you to suit the firewall to your needs better.
> 
> This script requires you to fill out the variables in configuration.sh to your preference before running it. Otherwise it will not work as it is missing information is requires to properly set the firewall.

After the configuration.sh file is set with your configuration, this is what running launch.sh looks like

![iptables script](/images/bash-script-screenshots/iptables-00.png)

If your rule changes sever your connection, say you were on SSH but forgot to allow it, automatically your most recent rules will be re-applied.

![iptables script](/images/bash-script-screenshots/iptables-02.png)

By default before each time the script is ran your existing rules are saved. There is a companion script called 'restore-iptables.sh' that will restore your iptables rules back to those before the new rules were set. It saves an original copy of your rules the first time the script is ran, so long as that file exists still it will create time and date stamped rules files each time after to give you a selection of which point in time to restore your rules to.

![iptables script](/images/bash-script-screenshots/iptables-01.png)

----------

* [lazyGit](https://github.com/jeekkd/lazyGit "https://github.com/jeekkd/lazyGit")
    

This script is super simple, it is intended to make basic git usage even easier and quicker then it already is by launching the script following a couple prompts, done.

![lazygit script](/images/bash-script-screenshots/lazygit.png)

----------

* [rsync-backup](https://github.com/jeekkd/rsync-backup "https://github.com/jeekkd/rsync-backup")
    
A rsync backup script to be ran scheduled by cron. It supports logging and backing up to both network share and locally attached storage which can be set in a variety of combinations

Log file example:

```
rsync backup beginning at Tue Oct 11 22:43:37 CDT 2016
2016/10/11 22:43:37 [4451] building file list
2016/10/11 22:44:03 [4451]>f.sT...... Scripts/rsync_backup.sh
2016/10/11 22:44:03 [4451]>f+++++++++ Scripts/syncDrives.sh
2016/10/11 22:44:03 [4451] sent 346090267 bytes  received 5476 bytes  total size 4758013455
Backup to network share beginning
2016/10/11 22:44:03 [4457] building file list
2016/10/11 22:44:33 [4457]>f.sT...... Scripts/rsync_backup.sh
2016/10/11 22:44:33 [4457]>f+++++++++ Scripts/syncDrives.sh
2016/10/11 22:44:33 [4457] sent 346090267 bytes  received 5476 bytes  total size 4758013455
Backup completed at:        Tue Oct 11 22:44:33 CDT 2016
mountChoice was:             3
backupDrive was:             /dev/sdb1
Drive backup exit code:      0
backupShare was:             /media/<Your network share>/<Your backup directory>
Share backup exit code:      0
backupDir was:               /media/<Your backup drive>/<Your backup directory>
The backup to /media/<Your network share>/<Your backup directory> was successful and completed without error
The backup to /dev/sdb1 mounted at /media/data was successful and completed without error
```

----------

* [Dgis](https://github.com/jeekkd/dgis "https://github.com/jeekkd/dgis")
    
This is a script to install Gentoo in a partially interactive manner, it gives a baseline install configured with Gentoo itself, a kernel, bootloader, some necessary applications, etc. while giving the flexibility to be prompted for desktop environment, display manager, along with locale, time zone, usernames, passwords, hostname and other user selections.

The intended use is that first your partitioning is done as you wish, you run the script and it handles everything up the point of configuring the fstab or any additional options your grub configuration may require.

