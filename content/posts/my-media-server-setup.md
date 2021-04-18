+++
author = "Daulton"
title = "My Media Server Setup"
date = "2018-12-19"
description = "I describe my home media server setup to give some insight into a well oiled setup."
tags = [
    "blog",
]
categories = [
    "blog",
]
+++

I have a nice media server setup which has served me well, I would like to share some detail about it so others can gain some useful insight into the setup of a well oiled media server.
<!--more-->

The base of my media server is FreeNAS which is very useful as it is a NAS but it also makes jail usage very easy and you can give jails access to any particular directory on a storage pool. 

Updated 01-2020: I have updated this post to reflect my change from Plex Media Server to Jellyfin.

#### Jails

I have jails for the following services:

* [NZBHydra](https://github.com/theotherp/nzbhydra)
  * A multi NZB index search host, meaning it can search multiple sources at once.
* [Sonarr](https://sonarr.tv/)
  * NZB based TV series monitor and downloader.
  * Not yet released movies can be added and set to be monitor for releases that set your quality presets. This quality preset can be set per movie.
  * Can search with NZBHydra and download with SABnzbd. It also support torrents and I have had it hooked in with my Deluge server before and it worked well.
* [Radarr](https://radarr.video/)
  * Same as Sonarr except it is for movies.
* [Headphones](https://github.com/rembo10/headphones)
  * This is similar to Sonarr except it is for music.
  * Your mileage will vary with music and NZB, as a general rule there is not a great deal of music that lasts on usenet but which indexer you use plays a role in how much is found. I personally have not had great results, I would stick to torrents for music personally.
* [SABnzbd](https://sabnzbd.org/)
  * The downloader that NZBHydra and Sonarr sends NZBs to.
* [Jellyfin](https://jellyfin.org/)
  * Media streaming server, capable of both music or video.
  * This is what streams the downloaded audio or video files that have been downloaded. Its a very nice way to watch media.
  * Perfect for an in house streaming solution for the HTPC.
  * Free and open source. Great UI. Easy install and maintenance. Packages are in distro repos.

---

#### Usenet choices

For my primary news service I went with [Thundernews](https://www.thundernews.com/). I have found them great, the speed has been consistent (150 Mb/s), few failed downloads, and the price is right at $3.99 USD monthly. The only con I can come up with is that it is apart of the Highwinds backend. 

For a secondary news service to fill in the blanks when there are missing fragments I went with a block from [UsenetExpress](https://www.usenetexpress.com/). The UsenetExpress block really does not get hit much and it is not strictly necessary even, but if you find yourself looking for more obscure things or what you look for has fragments often then a 500 Gb block should last you. 

My primary indexers are the following:

* NZB Tortuga
* NZB Planet
* Usenet Crawler
* Drunken Slug
* nzb.cat
* Tabula Rasa
* NZB Finder
* 6box

---

#### Media organization

I have my media organized in the following manner, within /mnt/pool0/media/Daultons I have folders named like such:

* Books
* TV Shows
* Movies
* Classical music
* Music
* Etc.

The useful thing about a split up structure is that you can do two useful things with it. One is that with Jellyfin libraries are created by setting a path to the media. The second is that you can set category tags in Sonarr, Headphones, etc, so that when it goes into your downloader (SABnzbd) it will then put them into a particular directory such as Movies. In the SABnzbd configuration I have some categories defined:

* Audio - /media/media/Daultons/Music
  * This is set in Headphones in Settings > Download settings > SABnzbd Category is set to this.
* Movies - /media/media/Daultons/Movies
  * This is set in Radarr within Settings > Download client > My download client is labeled sabnzbd > then enter movies in the category.
	 * Another option instead of Radarr is to download movies manually via searching within NZBhydra.
* TV - /media/media/Daultons/TV Shows/Downloads
  * This is set in Sonarr within Settings > Download client > My download client is labeled sabnzbd > then enter tv in the category.

Category configuration can be accessed in SABnzbd within settings > 

The directory path is different then shown above because the path /mnt/pool0/media/Daultons/ is mounted on the SABnzbd jail as /media/media.

---

#### Jellyfin libraries

Lastly, within Jellyfin I have the following Libraries created.

* Music
* Classical music
* Movies
* TV Shows

---

#### FreeNAS plugin advice

My experience with FreeNAS plugins has been less than great, I would advise against using plugins at least for Plex and Sonarr in particular. [Plex](https://daulton.ca/installing-plex-media-server-on-freebsd.html) and [Sonarr](https://github.com/Sonarr/Sonarr/wiki/Installation-FreeBSD) are simple to configure anyway so you wont be losing much and it will save headaches later.
