+++
author = "Daulton"
title = "My new backup solution"
date = "2022-05-05"
description = "I talk about finding my new backup solution, and how I use it."
tags = [
    "blog",
]
categories = [
    "blog",
]
+++

Hello! Long time no see, unfortunately! 

I have something to share, when I was recently looking into backup programs for Linux I ran into Borg, Restic, and Kopia.

<!--more-->

----------

I came across them in the past but back then found them too complicated and thought what was the appeal of a CLI backup program. I opted for a GUI program called Cloudberry Backup, its not bad - nice GUI, multiple jobs, supports of a lot of common backup destinations like S3, local, B2 storage, etc.

Cloudberry Backup has been good so far, except the backup speed is slow, with the free version there is a 100 GB limit, I am concerned about the longevity of the software, and you download and install a plain .rpm without a corresponding repository (no automated updates via your package manager). So, I figured it was finally time to take a look into the popular, robust, Linux backup programs available. I ended up comparing Borg, Restic, and Kopia to see which would suit my needs most. Please note each program has more pros and cons than these, I am listing the quick points of interest from my perspective. If you are also evaluating backup software I encourage you to take a deeper look as well.

Kopia
- A little too new for my liking.
- Less popular.
- Less information online, and docs felt incomplete.
- KopiaUI only supported backing up to one repository at a time.
- Not in OpenSUSE repos, I had to install the Flatpak.
- Supports all storage backends I would want.

Borg
- Borg must be running on the destination machine, meaning you wont have SFTP, B2, S3, etc. available.
- Very good deduplication.
- Mature and robust software.

Restic
- I like that it is written in Go.
- I like that is a single static binary, this gives me peace of mind I should always be able to run Restic and restore my files provided I have a compatible binary available in the future to run.
- Supports all storage backends I personally need, B2, S3, and local hard disk.
- Mature and robust software.

I ultimately ended up deciding to use Restic, it did not have quite as good deduplication as Borg but it is a mature and robust software that fits my needs and the backends I wish to use.

After some testing I came up with the following configuration. I installed Restic and Swaks from my distribution repository, wrote a script to handle automated backups of my files to each of the backend repositories I need, and then scheduled the backups to run on a user level systemd timer.

Warning! If you use my script and service file, read through everything below carefully and assure you adjust it to your setup.

Create the timer and service file under .config/systemd/user/ then run the below commands.

```
$  systemctl --user daemon-reload
$  systemctl --user enable restic-backup.timer
$  systemctl --all --user list-timers
```

restic-backup.timer
```
[Unit]
Description=Schedule restic backup script

[Timer]
#Execute job if it missed a run due to machine being off
Persistent=true
#Run after boot for the first time
OnBootSec=15min
#Run every x amount of time
OnUnitActiveSec=45m
#File describing job to execute
Unit=restic-backup.service

[Install]
WantedBy=timers.target
```

restic-backup.service
```
[Unit]
Description=Run Restic backup script

[Service]
Type=simple
ExecStart=/home/daulton/Files/Scripts/restic-backup.sh

[Install]
WantedBy=default.target
```

restic-backup.sh
<script src="https://gist.github.com/jeekkd/9adbd533db838b2c84ae8e05e50c4735.js"></script>
