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

## Publishing A Public PGP Key via HTTPS: <small>Web Key Directory (WKD)</small>

This document describes how to setup GnuPG Web Key Directory for an OpenPGP key.

<div class="alert alert-warning">
  <strong>Notice!</strong>
  WKD lookup is implemented in GnuPG since v2.1.12. It is enabled by default since 2.1.23.
</div>

An OpenPGP Web Key Directory is a method for users to discover the public key of a new contact.  The user requests the public key from the contacts organization maintains.  This differs from a [Key Server]() where a the user looks up a key on a 3rd party server, the server provides all keys that match requested address and the user must determine which key to use.  This practice bears the problem that the key-servers are not able to give a positive confirmation that a key actually belongs to the mail addresses given in the key.  Further, there are often several keys matching a mail address and thus one needs to pick a key on good luck.

GnuPG has a new key discovery scheme - Web Key Directory. Compared to previous schemes that relied on DNS, WKD can be easily deployed on any HTTPS server.

Web Key Directory is simply a lookup scheme that relies on HTTPS and correctly placed files on your web server.  No other software is required to run on the web server.

## Building the Web Key Directory Service

### Setting up the DNS Record (Optional)
<div class="alert alert-warning">
  <strong>Notice!</strong>
  The DNS SRV Records were removed in <a href="https://tools.ietf.org/rfcdiff?difftype=--hwdiff&url2=draft-koch-openpgp-webkey-service-07.txt">Draft 07</a> of the specification, may not work with all clients.
</div>

<pre>_openpgpkey._tcp.example.org.  IN  SRV 0 0 8443 wkd.example.org.</pre>

The target (in the example "wkd.example.org") MUST be a sub-domain of the domain-part (here "example.org").  The recommended name for the sub-domain in the [specification](https://tools.ietf.org/html/draft-koch-openpgp-webkey-service-06) is "wkd".

### Setting up the File System

Once complete the key/file must be accessible via a special URL constructed by appending `https://`, user domain, `/.well-known/openpgpkey/hu/` and a hash value.

For the key I will be using in this how-to the full URL should be: `https://mattrude.com/.well-known/openpgpkey/hu/d6tq6t4iirtg3qpyw1nyzsr5nsfcqrht`

So you must create the directory `.well-known/openpgpkey/hu/` inside the root of your html website.

For example, if you use the default Ubuntu config, you may simply run the following command.

<pre>mkdir -p /var/www/html/.well-known/openpgpkey/hu</pre>

### Setting up the Web Server

#### On Nginx

<pre>
    location ^~ /.well-known/openpgpkey {
        default_type        "text/plain";
        add_header          'Access-Control-Allow-Origin' '*' always;
    }
</pre>

#### On Apache

<pre>
    <Directory "/.well-known/openpgpkey">
        <IfModule mod_headers.c>
            Header set Access-Control-Allow-Origin "*"
        </IfModule>
    </Directory>
</pre>

#### On Lighttpd

<pre>setenv.add-response-header = ( "Access-Control-Allow-Origin" => "*" )</pre>

## Building a Single Public Key File

### Finding the hash to create the name with

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

## Building a Group of Public Key Files

Using the `generate-openpgpkey-hu` script, you can build your WKD from a GnuPG keyring you already have populated with keys.

First you must download the `generate-openpgpkey-hu` script.
<pre>curl -Las https://hg.intevation.de/gnupg/wkd-tools/raw-file/default/generate-openpgpkey-hu -o generate-openpgpkey-hu
chmod 755 generate-openpgpkey-hu</pre>

Once the script is downloaded and the permissions are set correctly, you are ready to start.



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

* OpenPGP Web Key Directory [Network Working Group Specification](https://tools.ietf.org/html/draft-koch-openpgp-webkey-service)
* More information may be found at [GnuPG Wiki](https://wiki.gnupg.org/WKD)
* The Web Key Directory Checkter found at [metacode.biz](https://metacode.biz/openpgp/web-key-directory)
