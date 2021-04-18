+++
author = "Daulton"
title = "Apache2 configuration for Dokuwiki"
date = "2018-08-28"
description = "Apache2 configuration for Dokuwiki"
tags = [
    "notes",
]
categories = [
    "notes",
]
+++

The following is the apache2 configuration file for Dokuwiki, running php7 on Ubuntu Server 16.04.
<!--more-->

----------

```
<VirtualHost *:80>
		ServerName daulton.ca
		ServerAlias www.daulton.ca

		#RedirectMatch permanent ^/(.*) https://daulton.ca/$1
		Redirect permanent / https://daulton.ca

		DocumentRoot /var/www/dokuwiki
		ErrorLog /var/log/apache2/error.log
		CustomLog /var/log/apache2/access.log combined
		UseCanonicalName Off

		<LocationMatch "/(data|conf|bin|inc)/">
				Order allow,deny
				Deny from all
				Satisfy All
		</LocationMatch>
</VirtualHost>

<IfModule mod_ssl.c>
<VirtualHost *:443>
		
		Protocols h2 http/1.1
		
		ServerName daulton.ca
		ServerAlias www.daulton.ca

		DocumentRoot /var/www/dokuwiki
		ErrorLog /var/log/apache2/error.log
		CustomLog /var/log/apache2/access.log combined
		UseCanonicalName Off

		SSLEngine on

		SSLCertificateFile      /etc/letsencrypt/live/daulton.ca/fullchain.pem
		SSLCertificateKeyFile /etc/letsencrypt/live/daulton.ca/privkey.pem

		 <Directory /var/www/>
				Options Indexes FollowSymLinks MultiViews
				AllowOverride all
				Order allow,deny
				allow from all
		</Directory>
		
		BrowserMatch "MSIE [2-6]" \
		nokeepalive ssl-unclean-shutdown \
		downgrade-1.0 force-response-1.0

		<LocationMatch "/(data|conf|bin|inc)/">
				Order allow,deny
				Deny from all
				Satisfy All
		</LocationMatch>
</VirtualHost>
</IfModule>
```
