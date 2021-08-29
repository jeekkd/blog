+++
author = "Daulton"
title = "Setup tt-rss server on OpenBSD"
date = "2020-02-02"
description = "This guide will help you set up tt-rss on OpenBSD. Tiny Tiny RSS is a free and open source web-based news feed (RSS/Atom) reader and aggregator."
tags = [
    "openbsd",
    "guides",
]
categories = [
    "guides",
]
+++

This guide will help you set up [tt-rss](https://tt-rss.org/) using PHP 7.3 and OpenBSD's httpd on OpenBSD 6.6. Tiny Tiny RSS is a free and open source web-based news feed (RSS/Atom) reader and aggregator.
<!--more-->

----------


1. Install the necessary packages, use the latest version of PHP available for PHP 7.3 when prompted.

```
# pkg_add -z php-7.3 php-pdo_pgsql-7.3 php-pgsql-7.3 php-curl-7.3 php-intl-7.3 postgresql-server git
```
 
2. In /etc/php-7.3.ini, add the following line under the “Dynamic Extensions” section:

```
extension=pdo_pgsql.so
extension=pgsql.so
extension=intl.so
extension=curl.so
zend_extension=opcache.so
```

3. Create a self-signed SSL certificate. Skip this skip if you will be using LetsEncrypt or have one purchased. Enter the details as prompted. This will create a 10 year, self signed, certificate.

```
# openssl genrsa -out /etc/ssl/private/server.key
# openssl req -new -x509 -key /etc/ssl/private/server.key -out /etc/ssl/server.crt -days 3365
```       

4. Edit /etc/httpd.conf with the following contents. Note, replace in the domain variable your own hostname and domain, note that the domain must resolve. If you do not have a record that is going to resolve to this host, put the IP here instead. In this example my hostname is tt-rss and my domain is example.com.

```
ext_addr="*"
domain="tt-rss.example.com"
prefork 3

server $domain {
	listen on $ext_addr port 80
	block return 301 "https://$SERVER_NAME$REQUEST_URI"
}

server $domain {
	root "/htdocs/tt-rss"
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
      
5. We need to copy a couple of files into the httpd chroot to make DNS work.
  
```
# mkdir -p /var/www/etc
# cp /etc/resolv.conf /var/www/etc
# cp /etc/hosts /var/www/etc
```
      
6. If you are installing PostgreSQL for the first time, you have to create a default database first.  In the following example we install a database in /var/postgresql/data with a dba account 'postgres' and scram-sha-256 authentication. We will be prompted for a password to protect the dba account, assure you save this somewhere safe.

```
# su - _postgresql
$ mkdir /var/postgresql/data
$ initdb -D /var/postgresql/data -U postgres -A scram-sha-256 -E UTF8 -W
```
      
7. While running as _postgresql still, start the postgresql server and then run exit to return to the root account.

```     
$ pg_ctl -D /var/postgresql/data -l logfile start
$ exit
```
      
8. Now setup the database and user for TTRSS (replace yourpasshere with some random password. Write it down somewhere, you are going to need it later.)

```  
# psql -U postgres

postgres=# CREATE USER "tt-rss" WITH PASSWORD 'YourPasswordHere';
postgres=# CREATE DATABASE ttrssdb WITH OWNER "tt-rss";
postgres=# \quit
```
       
9. Clone the tt-rss repo to get the latest master files, we will be placing them at /var/www/htdocs in the tt-rss directory.

```
# cd /var/www/htdocs
# git clone https://git.tt-rss.org/fox/tt-rss.git tt-rss
```
   
10. Now to set the properly set permissions and ownership of all of the files and directories for tt-rss. 
     
```
# chown -R www:www /var/www/htdocs/tt-rss
```
      
11. Copy system SSL certs into the chroot.

```    
# cp -R /etc/ssl/ /var/www/etc/
# rm -rf /var/www/etc/ssl/private/
```
         
12. Enable and start PHP and httpd now that we have done the configuration and generated the self-signed SSL cert.       

```
# rcctl enable httpd postgresql php73_fpm
# rcctl start httpd postgresql php73_fpm
```

13. For TTRSS to periodically check and update feeds we need to create a cron tab entry to do so every half an hour.

```
# crontab -u www -e
```
     
14. Then copy and paste the following into the crontab file.
 
```
*/30 * * * * /usr/local/bin/php-7.3 /var/www/htdocs/tt-rss/update.php --feeds --quiet
```

15. Load the web interface in your web browser by accessing https://your-ip. Enter the following on the installer page:

```
Database type: postgresql 
Username: tt-rss
Database name: ttrssdb
Port: 5432
Hostname: 127.0.0.1
Password for the tt-rss database user you entered above.
```

---

#### Notes:

* https://git.tt-rss.org/fox/tt-rss/wiki/InstallationNotes
 
