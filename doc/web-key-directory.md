---
layout: default
title: Publish A Public PGP Key via HTTPS&colon; Web Key Directory (WKD)
displaytitle: Publishing A Public Key via HTTPS&colon; <small>Web Key Directory (WKD)</small>
permalink: /guides/web-key-directory/
description: Web Key Directory (WKD) allows you to publish your OpenPGP key on your HTTPS server
tags: [wkd, Web Key Directory, pgp, gpg, GnuPG]
redirect_from:
  - /web-key-directory/
  - /wkd/
---

This document describes how to setup GnuPG Web Key Directory for an OpenPGP key.

An OpenPGP Web Key Directory is a method for users to discover the public key of a new contact.  The user requests the public key from the contacts organization maintains.  This differs from a [Key Server]() where a the user looks up a key on a 3rd party server, the server provides all keys that match requested address and the user must determine which key to use.  This practice bears the problem that the key-servers are not able to give a positive confirmation that a key actually belongs to the mail addresses given in the key.  Further, there are often several keys matching a mail address and thus one needs to pick a key on good luck.

GnuPG has a new key discovery scheme - Web Key Directory. Compared to previous schemes that relied on DNS, WKD can be easily deployed on any HTTPS server.

<div class="alert alert-warning" style="display:flex;">
  <strong>Notice!</strong>
  WKD lookup is implemented in GnuPG since v2.1.12. It is enabled by default since 2.1.23.
</div>

## Building the Web Key Directory Service

Web Key Directory is simply a lookup scheme that relies on HTTPS and correctly placed files on a web server.  No other software is required to run on the web server.

There are two methods of key discovery described in network working group specification [section 3.1 (Key Discovery)](https://tools.ietf.org/html/draft-koch-openpgp-webkey-service#section-3.1), the basic method and the advanced method.

These two methods are fundamentally the same.

The <b>Basic</b> method uses the domain address <code>https://example.com</code> while the <b>Advanced</b> method uses domain address like <code>https://openpgpkey.example.com</code>.

### Method 1: Basic WKD Service

#### Setting up the File System

Once complete the key/file must be accessible via a special URL constructed by appending `https://`, user domain, `/.well-known/openpgpkey/hu/` and a hash value.

For the key I will be using in this how-to the full URL should be: `https://mattrude.com/.well-known/openpgpkey/hu/d6tq6t4iirtg3qpyw1nyzsr5nsfcqrht`

So you must create the directory `.well-known/openpgpkey/hu/` inside the root of your html website.

For example, if you use the default Ubuntu config, you may simply run the following command.

<pre>mkdir -p /var/www/html/.well-known/openpgpkey/hu</pre>

#### Setting up the Web Server

**On Nginx**

<pre>
    location ^~ /.well-known/openpgpkey {
        default_type        "text/plain";
        add_header          'Access-Control-Allow-Origin' '*' always;
    }
</pre>

**On Apache**

<pre>
    <Directory "/.well-known/openpgpkey">
        $gt;IfModule mod_headers.c>
            Header set Access-Control-Allow-Origin "*"
        $gt;/IfModule>
    </Directory>
</pre>

**On Lighttpd**

<pre>    setenv.add-response-header = ( "Access-Control-Allow-Origin" => "*" )</pre>

### Method 2: Advanced  WKD Service

## Building a Single Public Key File

### Finding the local-part hash

After you have created the needed directories, you next need to find the hash of the UID you are going to use.  The simplest way of doing that is via the `--with-wkd` option.

<pre> $ gpg --list-keys --with-wkd 0x94c32ac158aea35c
pub   ed25519 2019-03-05 [SC] [expires: 2024-03-03]
      1B9910529DF4FE1FE3C6B03794C32AC158AEA35C
uid           [ultimate] Matt Rude <matt@mattrude.com>
              <strong>d6tq6t4iirtg3qpyw1nyzsr5nsfcqrht</strong>@mattrude.com
sub   cv25519 2019-03-05 [E] [expires: 2024-03-03]
</pre>

### Create the file

Now that you have UID hash, you are ready to go.

All you need to do is export your public key **binary** (not ASCII armored) file and place it as a correctly named file on your webserver.

So assuming that the root of your webserver is at `/var/www/html/`, you will run the following command.

<pre>$ gpg --export 0x94c32ac158aea35c > /var/www/html/.well-known/openpgpkey/hu/d6tq6t4iirtg3qpyw1nyzsr5nsfcqrht</pre>

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

<pre>$ gpg -v --auto-key-locate clear,wkd,nodefault --locate-key matt@mattrude.com
gpg: using pgp trust model
gpg: pub  ed25519/94C32AC158AEA35C 2019-03-05  Matt Rude <matt@mattrude.com>
gpg: key 94C32AC158AEA35C: public key "Matt Rude <matt@mattrude.com>" imported
gpg: Total number processed: 1
gpg:               imported: 1
gpg: auto-key-locate found fingerprint 1B9910529DF4FE1FE3C6B03794C32AC158AEA35C
gpg: <strong>automatically retrieved 'matt@mattrude.com' via WKD</strong>
pub   ed25519 2019-03-05 [SC] [expires: 2024-03-03]
      1B9910529DF4FE1FE3C6B03794C32AC158AEA35C
uid           [ unknown] Matt Rude <matt@mattrude.com>
sub   cv25519 2019-03-05 [E] [expires: 2024-03-03]
</pre>

If the key cannot be found via WKD or if it's in a wrong format (e.g. ASCII armored instead of binary) an error will be produced:

<pre>$ gpg --auto-key-locate clear,wkd,nodefault --locate-key matt@mattrude.com
gpg: error retrieving 'matt@mattrude.com' via WKD: No data
</pre>

**Importing a key via WKD**

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

## Web Key Service (WKS)

The Web Key Service (WKS) is a method for end users to send their public key via email to the WKD server.

The WKS stores a file the named `submission-address` inside the WKD folder structure.  A users email client then checks for this file, downloads it, and should find an email address.  The email client then check the WKD site for the public key of the submission address.   Assumming it finds a public key, it downloads the public key, then sends the users public key to the submission address via an encrypted email.  

Once the WKS receives the message, it stores the public key in the `pending` folder and sends an encrypted email back to the users email asking for them to confrim the request.

Once the user confrims the request, an email is sent back to the WKS service that proccess the confrimation and moves the public key from the pending folder to the `hu` folder.  Once the public key is in the `hu` folder, other users may start downloading it via WKD.

## Other WKD Resources

* OpenPGP Web Key Directory [Network Working Group Specification](https://tools.ietf.org/html/draft-koch-openpgp-webkey-service)
* More information may be found at [GnuPG Wiki](https://wiki.gnupg.org/WKD)
* The Web Key Directory Checkter found at [metacode.biz](https://metacode.biz/openpgp/web-key-directory)
