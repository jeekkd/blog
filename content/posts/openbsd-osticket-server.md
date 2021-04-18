+++
author = "Daulton"
title = "Setup osTicket server on OpenBSD"
date = "2019-06-24"
description = "This guide will help you set up the osTicket ticking system on OpenBSD. osTicket is a widely-used and trusted open source support ticket system. It seamlessly routes inquiries created via email, web-forms and phone calls into a simple, easy-to-use, multi-user, web-based customer support platform."
tags = [
    "openbsd",
    "guides",
]
categories = [
    "guides",
]
+++

This guide will help you set up [osTicket](https://osticket.com/) using Nginx and MariaDB on OpenBSD 6.5. osTicket is an Open-Source Helpdesk Ticketing System.
<!--more-->

osTicket is a widely-used and trusted open source support ticket system. It seamlessly routes inquiries created via email, web-forms and phone calls into a simple, easy-to-use, multi-user, web-based customer support platform. osTicket comes packed with more features and tools than most of the expensive (and complex) support ticket systems on the market.

----------


1. Install necessary packages such as nginx, php, mariadb, etc. Select PHP 7.3 when prompted.

```
pkg_add nginx unzip mariadb-server mariadb-client php php-mysqli php-cgi php-gd php-imap php-intl
```
     
2. Enable the services.
```
 
rcctl enable mysqld      
rcctl enable nginx
rcctl enable php73_fpm
```
       
3. Edit /etc/php-7.3.ini, uncomment the following line and change its value to 0:
 
```
cgi.fix_pathinfo=0
```
 
4. Again in /etc/php-7.3.ini, add the following line under the “Dynamic Extensions” section:

```
extension=mysqli.so
extension=gd.so
extension=imap.so
extension=intl.so
zend_extension=opcache.so
```

5. Setup MariaDB

```
mysql_install_db
rcctl start mysqld
mysql_secure_installation
```

6. Once the MariaDB setup is complete, connect with MySQL shell with the following command:

```
mysql -u root -p
```
    
7. Create a new database and user for osTicket, the database name is osticketdb and the username is osticket.
 
```
mysql> create database osticketdb;
mysql> create user osticket@localhost identified by 'create-a-strong-password here';
mysql> grant all privileges on osticketdb.* to osticket@localhost identified by 'create-a-strong-password here';
mysql> flush privileges;
mysql> exit;
```

8. This step is a number of small ones - we are creating a directory to serve osTicket from, changing directories to the newly created one, downloading and unarchiving osTicket and then changing the permissions. Please visit the [following page](https://github.com/osTicket/osTicket/releases) to see the current version, then in the commmands below make sure to use the latest version.
 
```
mkdir -p /var/www/htdocs/osticket
cd /var/www/htdocs/osticket
ftp https://github.com/osTicket/osTicket/releases/download/v1.12/osTicket-v1.12.zip
unzip osTicket-v1.12.zip
rm osTicket-v1.12.zip
cp upload/include/ost-sampleconfig.php upload/include/ost-config.php
chown -R www:www /var/www/htdocs/osticket
```

9. Create the Nginx config, save it as /etc/nginx/nginx.conf. Make sure you edit the server name and SSL cert as appropriate for your setup.
 
```
user  www;
worker_processes 4;

events {
	worker_connections  1024;
}

http {
	include         mime.types;
	default_type    application/octet-stream;
	sendfile        on;
	charset         utf-8;
	gzip            on;
	gzip_types      text/plain application/xml text/javascript;
	gzip_min_length 1000;

	index index.php index.html index.htm;

	# Rewrite all requests from HTTP to HTTPS
	server {
		listen 80;
		server_name osticket.daulton.ca;
		rewrite ^ https://osticket.daulton.ca permanent;
	}

	server {
		listen 443;
		server_name osticket.daulton.ca;
		ssl on;
		ssl_certificate /etc/ssl/server.crt;
		ssl_certificate_key /etc/ssl/private/server.key; 
		root /var/www/htdocs/osticket/upload;

		index index.php;
		client_max_body_size 2000M;
		client_body_buffer_size 100M;
		client_header_buffer_size 10M;
		large_client_header_buffers 2 10M;
 
		client_body_timeout 12;
		client_header_timeout 12;
		keepalive_timeout 15;
		send_timeout 10;
 
		set $path_info "";

		location ~ /include {
			deny all;
			return 403;
		}

		if ($request_uri ~ "^/api(/[^\?]+)") {
			set $path_info $1;
		}

		location ~ ^/api/(?:tickets|tasks).*$ {
			try_files $uri $uri/ /api/http.php?$query_string;
		}

		if ($request_uri ~ "^/scp/.*\.php(/[^\?]+)") {
			set $path_info $1;
		}

		location ~ ^/scp/ajax.php/.*$ {
			try_files $uri $uri/ /scp/ajax.php?$query_string;
		}

		location / {
			try_files $uri $uri/ index.php;
		}

		location ~ \.php$ {
			fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
			include        fastcgi_params;
			fastcgi_pass unix:/run/php-fpm.sock;
			fastcgi_param  PATH_INFO    $path_info;
		}
	}

}
```
        
10. Further setup for PHP FPM or else there will be an error and it will not start as this is not created by default but needed.

``` 
mkdir /etc/php-fpm.d
vi /etc/php-fpm.d/default.conf
```

Add the following:

```
;;;;;;;;;;;;;;;;;;;;
; Pool Definitions ;
;;;;;;;;;;;;;;;;;;;;

[www]
user = www
group = www
listen = /var/www/run/php-fpm.sock
listen.owner = www
listen.group = www
listen.mode = 0660
pm = dynamic
pm.max_children = 5
pm.start_servers = 2
pm.min_spare_servers = 1
pm.max_spare_servers = 3
chroot = /var/www
```
      
11. Remove the setup directory
 
```
rm -rf /var/www/htdocs/osticket/upload/setup
chmod 0644 /var/www/htdocs/osticket/upload/include/ost-config.php
```

12. Since osTicket with PHP is handling mailing it needs DNS to function, copy resolv.conf into the www chroot.

```
mkdir /var/www/etc
cp /etc/resolv.conf /var/www/etc/
```
        
13. Start/restart PHP and nginx now that things are prepared.
   
```     
rcctl restart php73_fpm nginx
```
       
12. Lastly, we need to add a new root crontab entry to setup email retrieval.
 
```
*/3 * * * * /usr/local/bin/php-7.3 /var/www/htdocs/osticket/upload/api/cron.php
```      

You will now be able to access your osTicket installation at the server_name you entered above in the nginx configuration. To access the agent portion of the system append /scp to end to make it like so: https://osticket.daulton.ca/scp
        
       
---

#### Notes:

* If your mail server or provider is not working in the mail settings, please try to use smtpd as outlined [in this guide for steps 5-7.](https://daulton.ca/wordpress-using-opensmtpd-for-mail-delivery/)
* Here is how to generate self-signed keys

```
openssl genrsa -out /etc/ssl/private/server.key
openssl req -new -x509 -key /etc/ssl/private/server.key -out /etc/ssl/server.crt -days 3365
```
