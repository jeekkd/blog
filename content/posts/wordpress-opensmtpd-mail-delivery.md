+++
author = "Daulton"
title = "Wordpress using OpenSMTPD for mail delivery"
date = "2019-04-09"
description = "Provides instruction of how to use OpenSMTPD for mail delivery with Wordpress."
tags = [
    "guides",
]
categories = [
    "guides",
]
+++

This guide provides instruction of how to use OpenSMTPD for mail delivery with Wordpress. The problem with Wordpress or the SMTP plugins for Wordpress is if the site is compromised the attacker has access to the credentials via what the web server user is able to access. But what if the credentials were stored outside of the http chroot and you just used an SMTP plugin to send to a local MTA which relayed to your mail server? That is what I will outline configuring.
<!--more-->

This guide requires your Wordpress host to be running a recent version of OpenBSD.

---

1. Change directories to your Wordpress plugins. If your wordpress directory name or location is different, adjust the path accordingly.
 
```
cd /var/www/htdocs/wordpress/wp-content/plugins
```

2. Download and unzip the [Post SMTP Wordpress plugin](https://wordpress.org/plugins/post-smtp/). Install the unzip package if you do not already have it.

```
ftp https://downloads.wordpress.org/plugin/post-smtp.1.9.8.zip
unzip post-smtp.1.9.8.zip
```

3. Change the permissions for wp-content to something more secure. Note, replace www with your web server user if you use something other then www.

```
chown -R www:www /var/www/htdocs/wordpress/wp-content/         
chmod 500 /var/www/htdocs/wordpress/wp-content/ 
```

4. Setup the secrets file for your mail user for OpenSMTPD to use. Replace no-reply with your desired email to send mail as, and mx.example.com with your mail server.

```
touch /etc/mail/secrets
chmod 640 /etc/mail/secrets
chown root:_smtpd /etc/mail/secrets
echo "no-reply no-reply@example.com:password goes here" > /etc/mail/secrets
```

5. Setup the /etc/mail/smtpd.conf. Again replace no-reply with your desired email to send mail as, and mx.example.com with your mail server.

```
table aliases file:/etc/mail/aliases
table secrets file:/etc/mail/secrets

listen on lo0

action "local" mbox alias <aliases>
action "relay" relay host smtp+tls://no-reply@mx.example.com:587 \
		auth <secrets>

match for local action "local"
match for any action "relay"
```

6. Restart smtpd since we make changes to the configuration.
 
```
rcctl enable smtpd
rcctl restart smtpd
```

7. In your Wordpress dashboard, activate the post smtp plugin then launch the wizard. Set the following as it asks you in the wizard:

```
Hostname: 127.0.0.1
Port Number: 25
Authentication Required: No
```

These settings work since this is just for the local MTA, not to your mail server. When the mail is relayed from the local MTA to your mail server it uses TLS on port 587 using the username and password you entered in step 4.
