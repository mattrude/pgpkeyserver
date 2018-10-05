---
layout: default
title: OpenPGP Web Key Directory (WKD)
permalink: /guides/web-key-directory/
description: Web Key Directory (WKD) allows you to publish your OpenPGP key on your HTTPS server
tags: wkd, Web Key Directory, pgp, gpg, GnuPG
redirect_from:
  - /web-key-directory/
  - /wkd/
---

This document describes how to setup Web Key Directory for an OpenPGP key.

Modern GnuPG has a new key discovery scheme - Web Key Directory. Compared to previous schemes that relied on DNS, WKD can be easily deployed on any HTTPS server.

## Creating the key file

Start out by exporting your public key as an **binary** (not ASCII armored) public key, to a file on your webserver.

<pre>$ gpg --export 0xDD23BF73 > /var/www/html/.well-known/openpgpkey/hu/d6tq6t4iirtg3qpyw1nyzsr5nsfcqrht</pre>

The key/file must be accessible via special URL constructed by appending `https://`, user domain, `/.well-known/openpgpkey/hu/` and a hash value.

Hash value can be obtained by listing the key with `--with-wkd` option:

<pre>
$ gpg --list-keys --with-wkd 0xDD23BF73
pub   rsa4096 2014-06-21 [SCEA]
      AE7384272B91AD635902320B27143AFFDD23BF73
uid           [ unknown] Matt Rude <matt@mattrude.com>
              <strong>d6tq6t4iirtg3qpyw1nyzsr5nsfcqrht</strong>@mattrude.com
uid           [ unknown] keybase.io/mattrude <mattrude@keybase.io>
              1idgzcdhugt49bmj1j1igmxpjz3w4h41@keybase.io
uid           [ unknown] [jpeg image of size 12114]
</pre>

For that key the full URL is:

https://mattrude.com/.well-known/openpgpkey/hu/d6tq6t4iirtg3qpyw1nyzsr5nsfcqrht

### Testing key discovery

GnuPG can be instructed to force discovery of the key via WKD even if it is locally present:

<pre>$ gpg --auto-key-locate clear,wkd,nodefault --locate-key matt@mattrude.com
gpg: key 27143AFFDD23BF73: public key "Matt Rude <matt@mattrude.com>" imported
gpg: Total number processed: 1
gpg:               imported: 1
pub   rsa4096 2014-06-21 [SCEA]
      AE7384272B91AD635902320B27143AFFDD23BF73
uid           [ unknown] Matt Rude <matt@mattrude.com>
uid           [ unknown] keybase.io/mattrude <mattrude@keybase.io>
uid           [ unknown] [jpeg image of size 12114]
</pre>

If the key cannot be found via WKD or if it's in a wrong format (e.g. ASCII armored instead of binary) an error will be produced:

<pre>$ gpg --auto-key-locate clear,wkd,nodefault --locate-key matt@mattrude.com
gpg: error retrieving 'matt@mattrude.com' via WKD: No data
</pre>

## Importing a key via WKD

You may run the following command to import your key into your key ring. Just change **matt@mattrude.com** to the email address you wish to import.

As `wkd` is the default key discovery method there is no need to use the `--auto-key-locate` option.

<pre>$ echo "Test message" | gpg -ear <strong>matt@mattrude.com</strong>
gpg: key DD23BF73: public key "Matt Rude <matt@mattrude.com>" imported
gpg: Total number processed: 1
gpg:               imported: 1  (RSA: 1)
gpg: DD23BF73: There is no assurance this key belongs to the named user

pub  4096R/DD23BF73 2014-06-21 Matt Rude <matt@mattrude.com>
 Primary key fingerprint: AE73 8427 2B91 AD63 5902  320B 2714 3AFF DD23 BF73

It is NOT certain that the key belongs to the person named
in the user ID.  If you *really* know what you are doing,
you may answer the next question with yes.

Use this key anyway? (y/N) <strong>y</strong></pre>

## Other WKD Resources

* More information may be found at [GnuPG Wiki](https://wiki.gnupg.org/WKD)
