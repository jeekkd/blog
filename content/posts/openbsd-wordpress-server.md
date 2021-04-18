+++
draft = false
author = "Daulton"
title = "Wordpress server setup on OpenBSD"
date = "2020-03-04"
description = "The steps for how to setup a Wordpress server on OpenBSD."
tags = [
    "openbsd",
    "guides",
]
categories = [
    "guides",
]
+++

I go over how to setup a Wordpress server on OpenBSD. OpenBSD is very stable and secure so its a great platform for a platform like Wordpress, especially combined with OpenBSD's httpd as it is a chrooted web server by default.

<!--more-->

This is done with OpenBSD's httpd, PHP 7.3, MariaDB 10.3, on OpenBSD 6.6, but the same steps will be applicable to future versions with minor adjustments due to PHP version changes.

---

1. Install the necessary packages.

```
pkg_add -z php-7.3* php-gd-7.3* php-mysqli-7.3* php-fastcgi-7.3* php-soap-7.3* php-curl-7.3* php-zip-7.3* mariadb-server mariadb-client wget unzip pecl73-imagick-3.4.4
```

2. Enable the web server, PHP, database, etc. to start at boot.

```
rcctl enable httpd mysqld php73_fpm
```

3. Download the latest Wordpress files.

```
wget https://wordpress.org/latest.zip
```

4. As the Wordpress files come zipped we now need to unzip them to be able to use it.

```
unzip latest.zip
```

5. We are done with the `latest.zip` file now, remove it.

```
rm latest.zip
```

6. Move the unzipped directory to htdocs where we will setup httpd to serve it.

```
mv wordpress/ /var/www/htdocs/
```

7. For security purposes instead of using the `nobody` user or the `www` user, lets make a separate user that is designated for only Wordpress files.

```
useradd -s /sbin/nologin -d /nonexistent -L daemon -u 446 wp
```

8. Set the permissions properly on the Wordpress files.

```
find /var/www/htdocs/wordpress -type d -exec chmod 755 {} \;
find /var/www/htdocs/wordpress -type f -exec chmod 644 {} \;
chown -R wp:wp /var/www/htdocs/wordpress/*
chown -R www:www /var/www/htdocs/wordpress/wp-content/
chmod 500 /var/www/htdocs/wordpress/wp-content/themes/
```

9. Let us generate a self-signed cert for this, but on a production setup you would use Lets Encrypt. Check into acme-client in the man pages for more information.

```
openssl genrsa -out /etc/ssl/private/server.key
openssl req -new -x509 -key /etc/ssl/private/server.key -out /etc/ssl/server.crt -days 3365
```

10. Edit `/etc/httpd.conf` with the following contents. Note, replace in the domain variable your own domain. In this example my domain is example.com.

```
ext_addr="*"
domain="example.com"
prefork 3

server $domain {
	listen on $ext_addr port 80
	block return 301 "https://$SERVER_NAME$REQUEST_URI"
}

server $domain {
	root "htdocs/wordpress"
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
     
11. We need to copy a couple of files into the httpd chroot to make DNS work.
  
```
mkdir -p /var/www/etc
cp /etc/resolv.conf /var/www/etc
cp /etc/hosts /var/www/etc
```

12. Edit `/etc/php-7.3.ini` and add the following line under the “Dynamic Extensions” section:

```
extension=mysqli.so
extension=curl.so
extension=soap.so
extension=imagick.so
zend_extension=opcache.so
```

13. Setup MariaDB, when you run `mysql_secure_installation` answer y to all of the prompts.

```
mysql_install_db
rcctl start mysqld
mysql_secure_installation
```

14. Setup the Wordpress database. This will create a database named wordpress_db and a user named wp_user. Note, assure to set user_password to your desired password and make sure it is strong!

```
mysql -u root -p
CREATE DATABASE wordpress_db;
CREATE USER 'wp_user'@'127.0.0.1' IDENTIFIED BY 'user_password';
GRANT ALL PRIVILEGES ON wordpress_db.* TO 'wp_user'@'127.0.0.1';
FLUSH PRIVILEGES;
EXIT;
```

15. Restart the services since a lot of configuration changes have been made.

```
rcctl restart httpd mysqld php73_fpm
```

16. At this stage you can how access https://example.com (Replace with your own domain name) to do the Wordpress install. When entering the database details, for `Database Host` enter `127.0.0.1` instead of `localhost`.

17. The installer will say the following, copy the generated `wp-config.php` and save it as `/var/www/htdocs/wordpress/wp-config.php`. After you save the file click "Run the installation".

> Sorry, but I can’t write the wp-config.php file.
> 
> You can create the wp-config.php file manually and paste the following text into it.
> 
> After you’ve done that, click “Run the installation”.

18. After the installation, again adjust Wordpress permissions:

```
chmod 440 /var/www/htdocs/wordpress/wp-config.php
chown www:www /var/www/htdocs/wordpress/wp-config.php
```
