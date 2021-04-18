+++
author = "Daulton"
title = "Installing Drupal on OpenBSD"
date = "2019-02-17"
description = "Provides instruction of how to install Drupal CMS, version 8, on OpenBSD 6.4 using Postgresql and Nginx."
tags = [
    "openbsd",
    "guides",
]
categories = [
    "guides",
]
+++

This guide provides instruction of how to install Drupal CMS, version 8, on OpenBSD 6.4 using Postgresql and Nginx. There are additional steps you should look into such as permissions for your Drupal installation.
<!--more-->

---

1. Install required packages. Take your choice of PHP version, however I have tested only with php-7.0.32p1. In step number 6 and 7 adjust the file path accordingly based on the PHP version you choose.

```       
pkg_add libxml php xz php-gd png jpeg gettext libiconv postgresql-server php-pgsql php-pdo_pgsql nginx
```

2. These packages are only needed during installation and initial setup of additional plugins and/or themes. They can be later removed.

```        
pkg_add wget unzip
```

3. Setup the database and create a drupal user. 

```
# su - _postgresql
$ mkdir -p /var/postgresql/data
$ initdb -D /var/postgresql/data -U postgres -A scram-sha-256 -E UTF8 -W
$ exit
# rcctl -f start postgresql
# createuser -U postgres --pwprompt --no-superuser --createdb --no-createrole drupal
# createdb -U drupal -E UTF8 drupal
``` 
    
4. For the nginx config you can refer to [Nginx config generator](https://nginxconfig.io/) to generate a config. A helpful reference is also [the Drupal recipe on the nginx website](https://www.nginx.com/resources/wiki/start/topics/recipes/drupal/).
 
```
vi /etc/nginx/nginx.conf
```

5. Enable the services to start on boot

```
rcctl enable nginx php70_fpm postgresql
```       

6. Edit /etc/php-7.0.ini and add the following line under the “Dynamic Extensions” section:

```
extension=pgsql.so
extension=pdo_pgsql.so
extension=gd.so
extension=opcache.so
```

7. Then change the timezone as well if desired in /etc/php-7.0.ini

```
date.timezone = Canada/Central
```

8. Download latest Drupal

```
wget https://www.drupal.org/download-latest/zip
```    

9. Unzip or expand the Drupal zip we just downloaded
  
```     
unzip zip
```
        
10. Delete the left over zip file
   
```
rm zip
```
 
11. Copy the Drupal folder to the htdocs folder and also rename it to just Drupal
 
```
cp -R drupal-8.6.9/ /var/www/htdocs/
mv /var/www/htdocs/drupal-8.6.9/ /var/www/htdocs/drupal
```

12. Set the ownership of the Drupal files to the web server user
    
```    
chown -R www:www /var/www/htdocs/drupal
```

13. Start the nginx and php services
 
```       
rcctl start nginx php70_fpm postgresql
```

---

At this point you can access http://server name or http://server IP and complete the installation.
