---
layout: post
title:  "Dovecot and Postfix setup on FreeBSD"
tags: systems
---

This is my basic setup for Dovecot and Postfix under FreeBSD. I use [LMTP](https://en.wikipedia.org/wiki/Local_Mail_Transfer_Protocol) for local
mail delivery, that means all received email goes directly to an account on the system.

SMTP Authentication and secured connections with the server are also required.

All the mail is stored in a Maildir style folders.

- SMTP Authentication
- SSL/TLS [Take a look on Let's Encrypt for a free certificate](https://letsencrypt.org/)
- LMTP
- Maildir

First of all you need to [disable sendmail](https://www.freebsd.org/doc/en_US.ISO8859-1/books/handbook/mail-changingmta.html).

When done your file _rc.conf_ should look like the following.

_/etc/rc.conf_
```
postfix_enable="YES"
dovecot_enable="YES"
sendmail_enable="NO"
sendmail_submit_enable="NO"
sendmail_outbound_enable="NO"
sendmail_msp_queue_enable="NO"
saslauthd_enable="YES"
```

Now we can install whe required packages.

`pkg install dovecot`
`pkg install postfix-sasl-3.3.1_1,1`

The first file we need to modify is postfix _main.cf_. Be sure you
have configured dovecot as your authentication mechanism for SMTP and your
certificates are correctly set up and updated.

Set your domain correctly according to the domain you want to use for email.

In the first line you can check lmtp is the preferred mailbox transport.

_/usr/local/etc/postfix/main.cf_
```
myhostname = mail.yourdomain.com
mydomain = yourdomain.com
mailbox_transport = lmtp:unix:private/dovecot-lmtp
smtpd_sasl_auth_enable = yes
smtpd_sasl_path        = private/auth
smtpd_sasl_type        = dovecot
smtpd_recipient_restrictions =
  permit_mynetworks,
  permit_sasl_authenticated,
  reject_unauth_destination

smtpd_tls_cert_file=/usr/local/etc/letsencrypt/live/www.yourdomain.com/fullchain.pem
smtpd_tls_key_file=/usr/local/etc/letsencrypt/live/www.yourdomain.com/privkey.pem
smtpd_use_tls=yes
```

All the email addresses who had an empty header will have an email address like address@mail.yourdomain.com.

Next be sure your dovecot is using the lmtp protocol.

_/usr/local/etc/dovecot/dovecot.conf_
```
# Protocols we want to be serving.
protocols = imap pop3 lmtp
```

Now we need to restart the servers.

`service postfix restart`
`service dovecot restart`

Thats all you need to get email working with a basic configuration, just to
send and receive emails through your own domain.
