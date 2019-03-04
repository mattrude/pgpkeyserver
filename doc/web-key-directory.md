---
layout: default
title: Publish A Public PGP Key via HTTPS&colon; Web Key Directory (WKD)
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

### Setting up the Web Server

Once complete the key/file must be accessible via special URL constructed by appending `https://`, user domain, `/.well-known/openpgpkey/hu/` and a hash value.

For the key I will be using in this how-to the full URL is: `https://mattrude.com/.well-known/openpgpkey/hu/d6tq6t4iirtg3qpyw1nyzsr5nsfcqrht`

So you must create the directory `.well-known/openpgpkey/hu/` inside the root of your html website.

For example, if you use the default Ubuntu config, you may simply run the following command.

    mkdir -p /var/www/html/.well-known/openpgpkey/hu

### Finding the name

After you have created the needed directories, you next need to find the hash of the UID you are going to use.  The simplest way of doing that is via the `--with-wkd` option.

<pre>
$ gpg --list-keys --with-wkd 0xDD23BF73
pub   rsa4096 2014-06-21 [SCEA]
      AE7384272B91AD635902320B27143AFFDD23BF73
uid           [ unknown] Matt Rude <matt@mattrude.com>
              <strong>d6tq6t4iirtg3qpyw1nyzsr5nsfcqrht</strong>@mattrude.com
</pre>

### Create the file
Now that you have UID hash, you are ready to go.

All you need to do is export your public key **binary** (not ASCII armored) file and place it as a correctly named file on your webserver.

So assuming that the root of your webserver is at `/var/www/html/`, you will run the following command.

<pre>$ gpg --export 0xDD23BF73 > /var/www/html/.well-known/openpgpkey/hu/d6tq6t4iirtg3qpyw1nyzsr5nsfcqrht</pre>

For that key the full URL is:

https://mattrude.com/.well-known/openpgpkey/hu/d6tq6t4iirtg3qpyw1nyzsr5nsfcqrht

## Testing key discovery

GnuPG can be instructed to force discovery of the key via WKD even if it is locally present:

<pre>$ gpg --auto-key-locate clear,wkd,nodefault --locate-key matt@mattrude.com
gpg: key 27143AFFDD23BF73: public key "Matt Rude <matt@mattrude.com>" imported
gpg: Total number processed: 1
gpg:               imported: 1
pub   rsa4096 2014-06-21 [SCEA]
      AE7384272B91AD635902320B27143AFFDD23BF73
uid           [ unknown] Matt Rude <matt@mattrude.com>
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
* The Web Key Directory Checkter found at [metacode.biz](https://metacode.biz/openpgp/web-key-directory)

