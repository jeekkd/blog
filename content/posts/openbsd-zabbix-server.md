+++
draft = false
author = "Daulton"
title = "Zabbix server setup on OpenBSD"
date = "2020-03-02"
lastmod = "2020-04-25"
description = "The steps for how to setup a Zabbix monitoring and alerting server on OpenBSD."
tags = [
    "openbsd",
    "guides",
]
categories = [
    "guides",
]
+++

I go over how to setup a Zabbix monitoring and alerting server on OpenBSD. OpenBSD is very stable and secure so its a great platform for a system like Zabbix. For this configuration we use OpenBSD's httpd, PHP 7.3, and PostgreSQL, on OpenBSD 6.6.

<!--more-->

---

1. Install the necessary Zabbix, PHP, and PostgreSQL packages.

```
pkg_add -z zabbix-web zabbix-agent zabbix-server-*-pgsql php-pgsql-7.3* postgresql-server postgresql-client fping
```

2. Enable the web server, PHP, Zabbix, etc. to start at boot.

```
rcctl enable httpd saslauthd postgresql php73_fpm zabbix_agentd zabbix_server
```

3. Adjust the following sysctl values, we are going to put them in sysctl.conf so they remain persistent across reboots. On OpenBSD the default shared memory value is too low for the Zabbix Server and Proxy to work properly.

> vi /etc/sysctl.conf

```
kern.shminfo.shmall=524288
kern.seminfo.semmni=60
kern.seminfo.semmns=1024
```

4. Add the following to the bottom of the login.conf file, with this configuration the zabbix processes have their own login(1) class with tuned resources, such as more open file descriptors etc.

> vi /etc/login.conf

```
zabbix_server:\
	:openfiles-cur=1024:\
	:openfiles-max=2048:\
	:tc=daemon

postgresql:\
	:openfiles=768:\
	:tc=daemon:
```

5. For reference you can keep the original Zabbix server and agent configuration around.

```
mv /etc/zabbix/zabbix_agentd.conf /etc/zabbix/zabbix_agentd.conf.original
mv /etc/zabbix/zabbix_server.conf /etc/zabbix/zabbix_server.conf.original
```

6. Add a simple Zabbix agent configuration on the Zabbix server itself so it can monitor itself.

> vi /etc/zabbix/zabbix_agentd.conf

```
# This is a configuration file for Zabbix agent daemon (Unix)
LogType=system
Server=127.0.0.1
ListenPort=10050
ListenIP=0.0.0.0
ServerActive=127.0.0.1
Hostname=<add current systems hostname here, the system which this agent is on>
```

7. If you are installing PostgreSQL for the first time, you have to create a default database first.  In the following example we install a database in /var/postgresql/data with a dba account 'postgres' and scram-sha-256 authentication. We will be prompted for a password to protect the dba account, assure you save this somewhere safe.

```
# su - _postgresql
$ mkdir /var/postgresql/data
$ initdb -D /var/postgresql/data -U postgres -A scram-sha-256 -E UTF8 -W
```
      
8. While running as _postgresql still, start the postgresql server and then run exit to return to the root account.

```     
$ pg_ctl -D /var/postgresql/data -l logfile start
$ exit
```

9. Now create the database and user for Zabbix (Assure to create a very strong password, and save it somewhere safe.)

```  
$ createuser -U postgres --pwprompt --no-superuser --createdb --no-createrole zabbix
$ createdb -U zabbix zabbix
```

And import initial schema and data:

```
$ cd /usr/local/share/zabbix/schema/postgresql
$ cat schema.sql | psql -U zabbix zabbix
$ cat images.sql | psql -U zabbix zabbix
$ cat data.sql | psql -U zabbix zabbix
```

10. This is the Zabbix server configuration itself, add the below file with the following contents except add your own password to DBPassword.

> vi /etc/zabbix/zabbix_server.conf

```
LogType=system
PidFile=/var/www/var/run/zabbix/zabbix_server.pid
SocketDir=/var/www/var/run/zabbix
DBHost=
DBName=zabbix
DBUser=zabbix
DBPassword=Replace this with your chosen zabbix database user password!!
StartDiscoverers=3
SNMPTrapperFile=/var/log/snmptrap/snmptrap.log
Timeout=4
AlertScriptsPath=/etc/zabbix/alertscripts
ExternalScripts=/etc/zabbix/externalscripts
FpingLocation=/usr/local/sbin/fping
Fping6Location=/usr/local/sbin/fping6
StartPingers=10
LogSlowQueries=3000
```

Note: Leave DBHost= empty for postgresql based installations.

11. Edit /etc/php-7.3.ini and add the following line under the “Dynamic Extensions” section:

```
extension=pgsql.so
extension=gd.so
zend_extension=opcache.so
```

Then in the same file change the following values, except put the time zone to your own of course.

```
date.timezone = Canada/Central
max_input_time = 300
max_execution_time = 300
post_max_size = 16M
```

12. Create a self-signed SSL certificate. Skip this skip if you will be using LetsEncrypt or have one purchased. Enter the details as prompted. This will create a 10 year, self signed, certificate.

```
# openssl genrsa -out /etc/ssl/private/server.key
# openssl req -new -x509 -key /etc/ssl/private/server.key -out /etc/ssl/server.crt -days 3365
```  

13. This is the web server configuration, edit /etc/httpd.conf and replace the current file contents with the following.

```
ext_addr="*"
domain="zabbix"
prefork 3

server $domain {
	listen on $ext_addr port 80
	block return 301 "https://$SERVER_NAME$REQUEST_URI"
}

server $domain {
	root "/zabbix"
	listen on $ext_addr tls port 443
	tls {
		key "/etc/ssl/private/server.key"
		certificate "/etc/ssl/server.crt"
	}

	# Increase connection limits to extend the lifetime
    connection { max requests 500, timeout 3600 }
    connection { max request body 8388608 }

    directory {
		index "index.php"
    }
    
    location "/conf/*" {
		block return 401
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

14. We need to copy a couple of files into the httpd chroot to make DNS work.
  
```
# mkdir -p /var/www/etc
# cp /etc/resolv.conf /var/www/etc
# cp /etc/hosts /var/www/etc
```

15. We must now set the ownership properly on all of the zabbix web files.

```
# chown -R www:www /var/www/zabbix/
```

16. In step one and two we installed the necessary packages and then enabled them to start on boot but now it time to start them as configuration is complete. 

```
rcctl start httpd postgresql php73_fpm zabbix_agentd zabbix_server
```

17. Lastly, edit the zabbix web config file with the database details. Make sure each of the values in the config reflect these ones below, make sure you use your own zabbix database user password.

> vi /var/www/zabbix/conf/zabbix.conf.php

```
$DB['TYPE']                     = 'POSTGRESQL';
$DB['SERVER']                   = '127.0.0.1';
$DB['PORT']                     = '5432';
$DB['DATABASE']                 = 'zabbix';
$DB['USER']                     = 'zabbix';
$DB['PASSWORD']                 = 'Replace this with your chosen zabbix database user password!!';
```

Connect to your newly installed Zabbix frontend in the web browser: https://server_ip. Zabbix default user name is Admin, password zabbix.

----

Notes

- For simplicity, reboot to have the sysctl changes come into effect, or in front of each value in step three do the following in the terminal: sysctl kern.seminfo.semmni=60
- Zabbix default user name is Admin, password zabbix. 
- Please be aware OpenBSD uses LibreSSL which does not support PSK, that means with Zabbix for agent traffic encryption you can only use cert based, or no encryption.

