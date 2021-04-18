+++
draft = false
author = "Daulton"
title = "Miniflux server setup on OpenBSD"
date = "2020-10-18"
lastmod = "2021-01-18"
description = "How to setup a Miniflux server on OpenBSD."
tags = [
    "openbsd",
    "guides",
]
categories = [
    "guides",
]
+++

I go over how to setup a [Miniflux](https://miniflux.app) server on OpenBSD. Miniflux is a minimalist and opinionated RSS feed reader. 

These steps should apply to OpenBSD 6.8 or greater.

<!--more-->

---

1. Miniflux uses PostgreSQL as the database, and we also need wget to download Miniflux so the first step is to install the packages.

```
pkg_add postgresql-server postgresql-contrib postgresql-client miniflux
```

2. Enable PostgreSQL and Miniflux to start at boot.

```
rcctl enable postgresql miniflux
```

3. If you are installing PostgreSQL for the first time, you have to create a default database first.  In the following example we install a database in /var/postgresql/data with a dba account 'postgres' and scram-sha-256 authentication. We will be prompted for a password to protect the dba account, assure you save this somewhere safe.

```
# su - _postgresql
$ mkdir /var/postgresql/data
$ initdb -D /var/postgresql/data -U postgres -A scram-sha-256 -E UTF8 -W
```
      
4. While running as _postgresql still, start the postgresql server and then run exit to return to the root account.

```     
$ pg_ctl -D /var/postgresql/data -l logfile start
$ exit
```

5. Now create the database and user for Miniflux (Assure to create a very strong password, and save it somewhere safe.)

```  
$ createuser -U postgres --pwprompt --no-superuser --createdb --no-createrole miniflux
$ createdb -U miniflux miniflux
```

6. Create the hstore extension with the postgres user as it has SUPERUSER privileges. This is needed by miniflux before the database initialization can happen or work. Run the commands and then run exit to return to the root account.

```
# su - _postgresql
$ psql miniflux postgres
> CREATE EXTENSION hstore;
> \q
$ exit
```

7. Miniflux needs a config file to control the variable to change the standard database name, username, and password, and also to make the web interface listen of any address instead of 127.0.0.1 only. Fill out the database password below to the miniflux database user password you set in step 5.

> vi /etc/miniflux.conf

```                                                                                              
LISTEN_ADDR=0.0.0.0:8080
DATABASE_URL=user=miniflux password=your password dbname=miniflux sslmode=disable
```

8. Run the SQL migrations to initialize the database.

```
miniflux -c /etc/miniflux.conf -migrate
```

9. Create an admin user. This is also used to login to the web interface as the first user.

```
miniflux -c /etc/miniflux.conf -create-admin
```

10. Now we are finally ready to start Miniflux!

```
rcctl -d start miniflux
```

11. You can now connect to the web interface at http://x.y.x.y:8080/

---

Change log:

- 01/17/2021: Fixed `CREATE EXTENSION hstore;` issue where `postgresql-contrib` needed to be installed and typos.
- 01/18/2021: Updated guide for OpenBSD 6.8, there is now a package available for Miniflux.
