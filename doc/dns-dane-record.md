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

GnuPG version [2.1.9](https://lists.gnupg.org/pipermail/gnupg-announce/2015q4/000380.html) and greater has a nice little function `--print-dane-records` that prints the dane record for you.

    gpg2 --print-dane-records -k user@example.com

You should receive an output similar to the text below.

    ;; pub  nistp256/03305F35 2015-02-15
    ;;      Key fingerprint = 77F1 D65B 5FF0 54DC 9286  6078 0314 CD85 0330 5F35
    ;; uid  Matt Rude <m@mattrude.com>
    $ORIGIN _openpgpkey.mattrude.com.
    ; 77F1D65B5FF054DC928660780314CD8503305F35
    ; Matt Rude <m@mattrude.com>
    62c66a7a5dd70c3146618063c344e531e6d4b59e379808443ce962b3 TYPE61 \# 433 (
        98520454e11f3413082a8648ce3d0301070203045add655ecef45f6c8eccdab7
        e3e844d3f7f5a2fc4b62410efd1e9f34efb7c63830478e7f93c593db4110b3cd
        19d175c10cce1ec630d567a265f5b0484b0d3c58b41a4d617474205275646520
        3c6d406d617474727564652e636f6d3e887e041313080026050254e11f34021b
        03050905a39a80050b09080702061508090a0b0203160201021e01021780000a
        09100314cd8503305f356da50100e0182d1397a9034bdb77dfe13b2a020fd530
        9023fe71671d6655745c19c9f0c000ff6417963dc6d1baec2dd320969e7410d1
        5b976c1da3e9ff01684a40983ba07b15b8560454e11f3412082a8648ce3d0301
        07020304a048456ab44f987d2711e3c4e067d863ada5a5fbd1824a14fd80d962
        2d5450041974175c2067571ef03a26e620f472056839b6d3132396858b847b0b
        b52ba41c03010807886704181308000f050254e11f34021b0c050905a39a8000
        0a09100314cd8503305f35fc2000fe2a81e6357fc29a33a3985564eccea99f78
        f181e3dc4ca27d020f0088a8265d8f0100e7d33a6508279fc9aa300a4416ba24
        7d3f4159bfdd3f72cb81cc4ecbf1c18e73
        )


### Using Web Methods

* https://www.huque.com/bin/openpgpkey

## Testing the DNS DANE Certificate

Using GnuPG 2.1.9+, you may run the following command to download and import the key to your key ring.

    gpg2 --auto-key-locate clear,dane -v --locate-key matt@mattrude.com

The output should be something similar to the text below.

<small><i>Note: the "unknown" in the "uid" line is due to the trust level of the key, and since we just downloaded it, there is no trust yet.</i></small>

    gpg: using PGP trust model
    gpg: pub  nistp256/03305F35 2015-02-15  Matt Rude <m@mattrude.com>
    gpg: key 03305F35: public key "Matt Rude <m@mattrude.com>" imported
    gpg: Total number processed: 1
    gpg:               imported: 1
    gpg: auto-key-locate found fingerprint 77F1D65B5FF054DC928660780314CD8503305F35
    gpg: automatically retrieved 'm@mattrude.com' via DANE
    pub   nistp256/03305F35 2015-02-15 [expires: 2018-02-14]
    uid         [ unknown] Matt Rude <m@mattrude.com>
    sub   nistp256/B3BFB866 2015-02-15 [expires: 2018-02-14]

## Other DNS DANE How-to Resources

* More information may be found at
[gushi](http://www.gushi.org/make-dns-cert/HOWTO.html),
[gnupg](https://lists.gnupg.org/pipermail/gnupg-users/2015-November/054725.html),
[ietf](http://tools.ietf.org/html/rfc6698),
