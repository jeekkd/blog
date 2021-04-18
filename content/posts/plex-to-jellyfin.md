+++
author = "Daulton"
title = "Plex to Jellyfin migration"
date = "2020-02-10"
lastmod = "2020-04-25"
description = "A couple of months ago I decided to make to move to [Jellyfin media server](https://jellyfin.org/) after being a long time [Plex](https://www.plex.tv/) user, here is why."
tags = [
    "blog",
]
categories = [
    "blog",
    "homelab",
]
+++

A couple of months ago I decided to make to move to [Jellyfin media server](https://jellyfin.org/) after being a long time [Plex](https://www.plex.tv/) user, here is why.
<!--more-->

I was content with Plex and for the most part it did exactly what I wanted, the subtitle handling, media detection, was all great, and it was not overly picky about file names or directory layout. There were some cons, such as:

* Proprietary software
* Lack of insight into what its doing and why when there is a problem.
* For me it started not being able to load playlists, it would give an error and it may happen one or twice more before the playlist would load and operate normally. This issue began happening when loading movies as well.
* The Android app was costly and the plex pass nag screen was getting difficult to bypass.
* The Plex database was getting very large, it was approximately 100 GB when I stopped using Plex. This may have been the reason for the prior issue.

The switch to Jellyfin was smooth, though it came with its own pros and cons. Let's start with the pro's:

#### Pros

* User local management with access permissions, meaning a user can be created in Jellyfin and access be added so that they can see only certain libraries.
* Fast interface and quick searches.
* Open source project.
* Android App is free.
* Sonnar and Radarr are able to integrate for Library updates, etc. using the Emby connector.
* Nice log files.

#### Cons

* [Directory layout needs to be fairly specific and file names need to be proper](https://jellyfin.org/docs/general/server/media/shows.html), otherwise media detection does not work right.
	* Due to this I do not put my music in Jellyfin, it would be too much work to organize properly.
* Subtitle fetching could be better, even with [Open Subtitles](https://jellyfin.org/docs/general/server/plugins/open-subtitles.html) and sources enabled I wish it would fetch more subtitles. About half of my movies are able to get subtitles.

Previously, I ran Plex Media Server in a jail on FreeNAS 11.2 U7 with direct access to the media storage. Now, the way I have Jellyfin setup is a Debian 10 virtual machine with NFS storage attached from my FreeNAS server. The virtual machine has 2 cores and 4 GB of RAM allocated.

Overall, I am happy with the change and the benefits are good for my uses.

