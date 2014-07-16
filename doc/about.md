---
layout: default
title: about
permalink: /doc/about/
---

## About this server
This site is maintained by Matt Rude [0xE9FFC093FD113A2E](http://keyserver.mattrude.com/pks/lookup?op=get&search=0xE9FFC093FD113A2E). If you would like to report any problems or bugs, please send a email to matt@mattrude.com, but only after reading our [FAQ](/doc/faq/).

This service may be withdrawn at any time and without notice to end-users. (Peers will be notified). End-users should use a pool definition, such as keys.gnupg.net which will alias into an operational pool.

**Note:** this service is provided free, to the public, in the hopes that it might prove useful. No warranty is provided, nor any offer of continuing service or access. By using this service, you MUST understand that presence of data in the keyserver (pools) in no way connotes trust. Anyone can generate a key, with any name or email address, and upload it. All security and trust comes from evaluating security at the ''object level'', via PGP Web-Of-Trust signatures. This keyserver makes it possible to retrieve keys, looking them up via various indices, but the collection of keys in this public pool is KNOWN to contain malicious and fraudulent keys. It is the common expectation of server operators that users understand this and use software which, like all known common OpenPGP implementations, evaluates trust accordingly. This expectation is so common that it is not normally explicitly stated.

## About the Server Pool

The main HKP pool which you should configure your keyserver software to use is ha.pool.sks-keyservers.net, or perhaps pool.sks-keyservers.net. Folks will understand and expect that choice. (Also na.pool.sks-keyservers.net or eu.pool.sks-keyservers.net).

Separately, as an experimental service which I do not expect folks to use, I run my own pool definition. I give it an obnoxiously long name specifically to discourage its use. This is keys.sks.pool.globnix.net (plus also keys.ipv4.sks.pool.globnix.net & keys.ipv6.sks.pool.globnix.net).

This keyserver pool was, to my knowledge, the first to use statistics to find reasonable means for inclusion independent of the count of keys on any given server. Although Kristian beat me to a geocoded pool, even though I was first to have geocoding of IPs collected in the spider server. A little competition is healthy.

In addition, as a feature copied from sks-keyservers.net, I have keys.ha.sks.pool.globnix.net (plus also keys.ipv4.ha.sks.pool.globnix.net & keys.ipv6.ha.sks.pool.globnix.net). My variant defines ‘ha’ as “either we got a Via: header, or we got a Server: header which was neither sks_www nor GnuKS”.

Similarly, with the same three variations in hostname, depending upon content of the mesh there may be entries for:

* keys.na.region.sks.pool.globnix.net
* keys.sa.region.sks.pool.globnix.net
* keys.eu.region.sks.pool.globnix.net
* keys.africa.region.sks.pool.globnix.net
* keys.asia.region.sks.pool.globnix.net
* keys.oceania.region.sks.pool.globnix.net

None of these currently have the proxy-in-front constraint; some do have the minimum version constraint. If no servers meet the selection criteria, then the entries will not exist in DNS (NXDOMAIN, rather than NOERROR).

This server is running [SKS Keyserver](https://bitbucket.org/skskeyserver/sks-keyserver/wiki/Home) written by Yaron Minsky.
