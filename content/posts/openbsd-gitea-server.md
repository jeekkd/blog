+++
author = "Daulton"
title = "Setup Gitea server on OpenBSD"
date = "2018-08-28"
description = "This guide will help you set up a Gitea server on OpenBSD"
tags = [
    "openbsd",
    "guides",
]
categories = [
    "guides",
]
+++

This guide will help you set up a Gitea server on OpenBSD 6.3 with PostgreSQL and using the built in OpenBSD httpd server. The following guide is still applicable to OpenBSD 6.4 without modification.
<!--more-->

Gitea is a a painless self-hosted Git service. Gitea is a community managed fork of Gogs, lightweight code hosting solution written in Go and published under the MIT license. It is also made with the intention of being easy to install, making it a great solution overall for a small-medium scale Git service. 


----------

1. Install the necessary packages
    
```
pkg_add -z go gitea postgresql-server postgresql-client
```

2. Edit /etc/rc.conf.local
    
```
relayd_flags=
pkg_scripts=postgresql gitdaemon gitea
```

3. Start the services
    
```
rcctl gitdaemon start
rcctl gitea start
```

4. Create the database
    
```
mkdir /var/postgresql/data
initdb -D /var/postgresql/data -U postgres -E UTF8 -A md5 -W
pg_ctl -D /var/postgresql/data start
createuser -U postgres --pwprompt --no-superuser --createdb --no-createrole gitea
createdb -U gitea gitea
```

5. Create the self-signed keys. Replace the IP 10.0.0.26 with the IP address of your own server. The purpose of this is so that relayd automatically detects and begins using them.
    
```
openssl genrsa -out /etc/ssl/private/10.0.0.26.key
openssl req -new -x509 -key /etc/ssl/private/10.0.0.26.key -out /etc/ssl/10.0.0.26.crt -days 3365
```

6. Configure /etc/relayd.conf. Note replace host value URL with your own as well as the listen on IP address.
    
```
table <gitea> { 10.0.0.26 }

http protocol "httpproxy" {

pass request quick header "Host" value "gitea.example.com" \
	forward to <gitea>

	block
}

relay "proxy" {
	listen on 10.0.0.26 port 443 tls 
	protocol "httpproxy"

	forward to <gitea> port 3000 
}
```

7. Start httpd
    
```
rcctl start relayd
```

8. Make required directory for Gitea
    
```
mkdir -p /usr/local/share/gitea/data/lfs
```

9. Access Gitea at the below  URL  and go through the installation
    
> https://ip address>

10. After the installation edit /etc/gitea/conf/app.ini as following
    
```
PROTOCOL               = http
DOMAIN                 = gitea.example.com
ROOT_URL               = https://gitea.example.com
HTTP_ADDR              = 127.0.0.1
HTTP_PORT              = 3000
LOCAL_ROOT_URL         = https://gitea.example.com/
SSH_DOMAIN             = gitea.example.com
```

11. Restart Gitea due to the app.ini changes
    
```
rcctl restart gitea
```

You should now be able to access your Gita instance at [https://gitea.example.com](https://gitea.example.com/ "https://gitea.example.com")

----------

Resources:

- [https://docs.gitea.io/en-us/config-cheat-sheet/](https://docs.gitea.io/en-us/config-cheat-sheet/ "https://docs.gitea.io/en-us/config-cheat-sheet/")
