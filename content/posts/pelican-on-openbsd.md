+++
author = "Daulton"
title = "Pelican on OpenBSD"
date = "2018-08-31"
description = "How to install Pelican on OpenBSD and setup a static site using httpd"
tags = [
    "openbsd",
    "guides",
]
categories = [
    "guides",
]
+++

The following guide will to be to install [Pelican](https://blog.getpelican.com/) on OpenBSD and using httpd. Pelican is a static site generator, written in Python, that requires no database or server-side logic.
<!--more-->

If we were to develop the website on a separate box or workstation, the static site could be served and managed using only things included with OpenBSD base. In this case, the development model would look like the following:

1. Write pages locally
2. Run `pelican content`
3. Tar the `projects/<your project name>/output` directory
4. Scp the tar to the web server box
5. Untar into `/var/www/htdocs/`

Of course you should test on a demo box before deploying to production, so that is encouraged to add those steps in there.

----------

1. Install Pelican

```
pkg_add -v pelican 
```

2. Depending upon your theme and plugin choices, the next step is optional. Read the README or descriptions to know what you need.

```
pkg_add -v py3-pip
ln -sf /usr/local/bin/pip3.6 /usr/local/bin/pip
pip install typogrify beautifulsoup4
```

Add this point Pelican is installed and ready to be used. Reference the documentation to get started, the [quickstart section](http://docs.getpelican.com/en/stable/quickstart.html) is very helpful.

3. Next follow the [following guide](https://www.romanzolotarev.com/openbsd/acme-client.html) ([Archive link](https://archive.fo/V7StS)) to get LetsEncrypt certs for use with httpd.

The following is my /etc/httpd.conf file after getting the cert initially:

```
prefork 6

server "www.daulton.ca" {
	listen on * tls port 443
	root "/htdocs/daulton.ca"
	tls {
			certificate "/etc/ssl/daulton.ca.fullchain.pem"
			key "/etc/ssl/private/daulton.ca.key"
	}
	location * {
			block return 301 "https://daulton.ca$REQUEST_URI"
	}
}

server "daulton.ca" {
	listen on * tls port 443
	tls {
			certificate "/etc/ssl/daulton.ca.fullchain.pem"
			key "/etc/ssl/private/daulton.ca.key"
	}
	location "/.well-known/acme-challenge/*" {
			root { "/acme", strip 2 }
	}
}

server "www.daulton.ca" {
	listen on * port 80
	alias "daulton.ca"
	block return 301 "https://daulton.ca$REQUEST_URI"
}
```

4. Enable and then start httpd

```
rcctl enable httpd
rcctl start httpd
```

Note: I recommend first getting things up and running where you are able to access a test page before trying to add a theme. After you get to that point successfully take a look at the following resources:

* [Pelican themes](http://pelicanthemes.com/) ([Github](https://github.com/getpelican/pelican-themes))
* [Pelican plugins](https://github.com/getpelican/pelican-plugins)

---

To make the demo deployment workflow quicker, I made the following script simply called `publish.sh` it may help you:

```
#!/bin/sh

homeDir=/home/daulton
projectURL=daulton.ca

rm -rf /var/www/htdocs/"$projectURL"/*
rm -rf "$homeDir"/projects/"$projectURL"/output/*
cd "$homeDir"/projects/"$projectURL"
pelican content
cp -R "$homeDir"/projects/"$projectURL"/output/* /var/www/htdocs/"$projectURL"
cp -R "$homeDir"/projects/"$projectURL"/images/ /var/www/htdocs/"$projectURL"
cp -R "$homeDir"/projects/"$projectURL"/documents/ /var/www/htdocs/"$projectURL"
chown -R www:www /var/www/htdocs/"$projectURL"
```

For working from your workstation and then copying the generated content to the web server for deployment here is another script:

```
#!/bin/sh

homeDir=/home/daulton/Files
projectURL=daulton.ca
sshUser=jeff
serverIP=45.56.45.124

rm -rf "$homeDir"/projects/"$projectURL"/output/*
cd "$homeDir"/projects/"$projectURL"
pelican content
cp "$homeDir"/projects/"$projectURL"/robots.txt "$homeDir"/projects/"$projectURL"/output/
cp -R "$homeDir"/projects/"$projectURL"/images/ "$homeDir"/projects/"$projectURL"/output/
cp -R "$homeDir"/projects/"$projectURL"/documents/ "$homeDir"/projects/"$projectURL"/output/
tar czfp /tmp/"$projectURL".tgz -C "$homeDir"/projects/"$projectURL"/output/ .

scp /tmp/"$projectURL".tgz "$sshUser"@"$serverIP":/tmp
ssh "$sshUser"@"$serverIP" "mkdir /tmp/$projectURL && tar xzf /tmp/"$projectURL".tgz -C /tmp/$projectURL"

echo
echo "Type: doas cp -R /tmp/$projectURL/* /var/www/htdocs/daulton.ca"
echo
ssh "$sshUser"@"$serverIP"
```

---

The following is my pelicanconf.py as an example and reference:

```
#!/usr/bin/env python
# -*- coding: utf-8 -*- #
from __future__ import unicode_literals

AUTHOR = 'Daulton'
SITENAME = 'Nix, Scripts, and Documentation'
SITEURL = 'https://daulton.ca'
DISPLAY_PAGES_ON_MENU = 'True'
MENUITEMS = (('Main', SITEURL),)
SITETITLE = 'Nix, Scripts, and Documentation'
SITESUBTITLE = 'Powered by Pelican, OpenBSD, and OpenBSD httpd'
SITEDESCRIPTION = 'Nix, Scripts, and Documentation' 

PLUGIN_PATHS = ['plugins']
PLUGINS = ['sitemap', 'extract_toc', 'tipue_search']
DIRECT_TEMPLATES = (('index', 'tags', 'categories','archives', 'search', '404'))
STATIC_PATHS = ['theme/images', 'images']
TAG_SAVE_AS = ''
CATEGORY_SAVE_AS = ''
AUTHOR_SAVE_AS = ''
DEFAULT_CATEGORY = 'Default'

THEME = 'theme/elegant'
PATH = 'content'
TIMEZONE = 'Canada/Central'
COPYRIGHT_YEAR = 2018
DEFAULT_LANG = 'English'

# Feed generation is usually not desired when developing
FEED_ALL_ATOM = None
CATEGORY_FEED_ATOM = None
TRANSLATION_FEED_ATOM = None
AUTHOR_FEED_ATOM = None
AUTHOR_FEED_RSS = None

# Social widget
SOCIAL = (('github', 'https://github.com/jeekkd'),
		('envelope','mailto:contact_daulton@pm.me')) 

# Elegant Labels
SOCIAL_PROFILE_LABEL = u'Stay in Touch'
RELATED_POSTS_LABEL = 'Keep Reading'
SHARE_POST_INTRO = 'Like this post? Share on:'

DEFAULT_PAGINATION = 10

# Landing page
LANDING_PAGE_ABOUT = { 'title': 'Welcome!', 
'details': """<div> This is a documentation based blog for Linux, *BSD, shell scripting, and documentation I write on various topics.

Take a look at the menu for links to the major topics, such as Shell Scripts, Guides, or BASH Guides! </div>""" }
```

