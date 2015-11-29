---
layout: default
title: OpenPGP DNS Dane Cert Records How-to
permalink: /guides/dns-dane-cert-records/
description: OpenPGP DNS DANE certificates allows you to publish your OpenPGP key to your DNS record.
tags: dane, pgp, gpg, GnuPG, DNS
---

## Publishing A Public PGP Key via DNS: <small>DANE Cert Records</small>

This document will walk you threw building a OpenPGP dane [dns] record.  

## Creating the DNS Record

There are multiple ways to create a OpenPGP DANE DNS Record, below we will talk about the two may ways.

### Using GnuPG 2.1.9+

GnuPG version [2.1.9](https://lists.gnupg.org/pipermail/gnupg-announce/2015q4/000380.html) and greater has a nice little function that prints the dane record for you.

    gpg2 --print-dane-records -k user@example.com

### Using Web Methods

* https://www.huque.com/bin/openpgpkey

## Testing the DNS DANE Certificate

Using GnuPG 2.1.9+, you may run the following command to download and import the key to your key ring.

    gpg2 --auto-key-locate clear,dane -v --locate-key matt@mattrude.com

The output should be something similar to the text below.

*Note: the "unknown" in the "uid" line is due to the trust level of the key, and since we just downloaded it, there is no trust yet.*

    gpg: using PGP trust model
    gpg: pub  rsa2048/95B0761F 2015-03-02  Matt Rude <matt@mattrude.com>
    gpg: key 95B0761F: public key "Matt Rude <matt@mattrude.com>" imported
    gpg: Total number processed: 1
    gpg:               imported: 1
    gpg: auto-key-locate found fingerprint 71FD20E328158C322133FBBEC4909EE495B0761F
    gpg: automatically retrieved 'matt@mattrude.com' via DANE
    pub   rsa2048/95B0761F 2015-03-02
    uid         [ unknown] Matt Rude <matt@mattrude.com>
    sub   rsa2048/BC158061 2015-03-02 [expires: 2016-03-01]

## Other DNS DANE How-to Resources

* More information may be found at
[gushi](http://www.gushi.org/make-dns-cert/HOWTO.html),
[gnupg](https://lists.gnupg.org/pipermail/gnupg-users/2015-November/054725.html),
[ietf](http://tools.ietf.org/html/rfc6698),
