---
layout: default
lang: en
title: Contact Me
permalink: /contact/
---

This site is maintained by Matt Rude ([0x27143affdd23bf73]({{ site.url }}/pks/lookup?search=0x27143affdd23bf73&fingerprint=on&hash=on&op=vindex)). If you would like to report any problems or bugs, please send a email to <matt@mattrude.com>, or use my <a href="#" onclick="javascript:window.open('https://encrypt.to/matt', '_blank', 'toolbar=no, scrollbars=no, resizable=yes, width=800, height=600');">secure contact form</a>.

### My Public PGP Key Information

**My Default RSA Key:**

    uid = Matt Rude <matt@mattrude.com>
    pub = 4096R/27143AFFDD23BF73 2014-06-21
    fingerprint = AE73 8427 2B91 AD63 5902  320B 2714 3AFF DD23 BF73

**My Elliptic Curve Cryptography (ECC) Key:**

    uid = Matt Rude <m@mattrude.com>
    pub = nistp256/03305F35 2015-02-15 [expires: 2018-02-14]
    fingerprint = 77F1 D65B 5FF0 54DC 9286  6078 0314 CD85 0330 5F35

Or, you may validate my keys using one of my [PKA]({{ site.url }}/guides/public-key-association/) DNS records.

### Signed Contact Information

A signed copy of this infromation may be found [here]({{ site.url }}/contact.txt), or using my [ECC key]({{ site.url }}/pks/lookup?search=0x0314CD8503305F35&fingerprint=on&hash=on&op=vindex), may be found [here]({{ site.url }}/contact-ecc.txt). You may validate these files by running the below commands:

    curl -s https://keyserver.mattrude.com/contact.txt |gpg --keyserver-options auto-key-retrieve --auto-key-locate pka --verify
    curl -s https://keyserver.mattrude.com/contact-ecc.txt |gpg2 --keyserver-options auto-key-retrieve --auto-key-locate pka --verify

You may also see my profile on [keybase.io/mattrude](https://keybase.io/mattrude).

<!--
## Secure Contact Form

<iframe height="600" width="100%" frameborder="0" src="https://encrypt.to/matt"></iframe>
-->
