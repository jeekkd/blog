+++
author = "Daulton"
title = "Setup DokuWiki on OpenBSD"
date = "2019-07-25"
description = "This guide will help you set up DokuWiki on OpenBSD. Easy to use, lightweight, standards-compliant wiki engine with a simple syntax allowing reading the data outside the wiki. All data is stored in plain files, therefore no database is required."
tags = [
    "openbsd",
    "guides",
]
categories = [
    "guides",
]
+++

This guide will help you set up [DokuWiki](https://www.dokuwiki.org/) using PHP 7.1 and OpenBSD's httpd on OpenBSD 6.5. 
<!--more-->

DokuWiki is a standards-compliant, simple-to-use wiki which allows users to create rich documentation repositories. It provides an environment for individuals, teams and companies to create and collaborate using a simple yet powerful syntax that ensures data files remain structured and readable outside the wiki.

----------

1. Install DokuWiki package.

```
pkg_add dokuwiki
```

2. In /etc/php-7.1.ini, add the following line under the “Dynamic Extensions” section:

```
extension=gd.so
zend_extension=opcache.so
```
     
3. In /etc/php-7.1.ini, adjust the following settings to be your preferred size or the example 15 MB. This controls of the maximum size of uploaded files.

```
; Maximum allowed size for uploaded files.
upload_max_filesize = 15M

; Must be greater than or equal to upload_max_filesize
post_max_size = 15M
```

4. Create a self-signed SSL certificate. Skip this skip if you will be using LetsEncrypt or have one purchased.

```
openssl genrsa -out /etc/ssl/private/server.key
openssl req -new -x509 -key /etc/ssl/private/server.key -out /etc/ssl/server.crt -days 3365
```    

5. Edit /etc/httpd.conf with the following contents. Note, replace in the domain variable your own hostname and domain. In this example my hostname is dokuwiki and my domain is example.com.

```
ext_addr="*"
domain="dokuwiki.example.com"
prefork 3

server $domain {
	listen on $ext_addr port 80
	block return 301 "https://$SERVER_NAME$REQUEST_URI"
}

server $domain {
	root "dokuwiki"
	listen on $ext_addr tls port 443

	connection max request body 15728640

	tls {
		key "/etc/ssl/private/server.key"
		certificate "/etc/ssl/server.crt"
	}
	directory {
		index "index.php"
	}
	location "*.php" {
		fastcgi socket "/run/php-fpm.sock"
	}
}

# Include MIME types instead of the built-in ones
types {
	include "/usr/share/misc/mime.types"
}
```
     
6. We need to copy a couple of files into the httpd chroot to make DNS work.

```
mkdir -p /var/www/etc
cp /etc/resolv.conf /var/www/etc
cp /etc/hosts /var/www/etc
```
    
7. Now to set the properly set permissions and ownership of all of the files and directories for DokuWiki. 

```   
find /var/www/dokuwiki -type d -exec chmod 700 {} \;
find /var/www/dokuwiki -type f -exec chmod 600 {} \;
chown -R www:www /var/www/dokuwiki
```
          
8. Enable and start PHP and httpd now that we have done the configuration and generated the self-signed SSL cert.       

```
rcctl enable httpd php71_fpm
rcctl start httpd php71_fpm
```

9. Proceed to complete the installation by opening install.php in your browser. Such as at https://dokuwiki.example.com/install.php, please note to replace dokuwiki and the domain in this URL and the httpd configuration above with your own hostname if you named this host something different.

10. Once you complete the installation in the previous step, its time to remove the install.php.
 
```
rm /var/www/dokuwiki/install.php 
```

---

#### Notes:

 - If you are upgrading your installation, don't forget to remove install.php.
 - The template (themes) directory is located at: /var/www/dokuwiki/lib/tpl
 - The plugins directory is located at: /var/www/dokuwiki/lib/plugins
 - User added data goes under: /var/www/dokuwiki/data
 
