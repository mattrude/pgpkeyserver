---
layout: default
lang: en
title: About
permalink: /about/
---

## What is PGP?

**PGP** (<b>P</b>retty **G**ood **P**rivacy) is a method of encrypting and signing data (for example an email) in a secure "_end to end_" way.  This means, the message is encrypted on your computer, using the recipient&rsquo;s public key, in a way that the e-mail server has no knowledge of the content of the message.  The recipient of the message then decrypts the message on their own computer using their private key.

Quoted from [RFC 4880](http://tools.ietf.org/html/rfc4880):

> OpenPGP software uses a combination of strong public-key and
> symmetric cryptography to provide security services for electronic
> communications and data storage.  These services include
> confidentiality, key management, authentication, and digital
> signatures.  This document specifies the message formats used in
> OpenPGP.

### PGP Resources

* [GnuPG Homepage](https://gnupg.org/) - The main location for the OpenPGP Standard
* [PGP Inc.](http://www.pgp.com/) - This historical home of PGP, but has since been sold to Symantec.
* [Email Self-defense](https://emailselfdefense.fsf.org/en/) - A teaching site to learn how and why you should use PGP for your electronic communication.
* [Wikipedia - Pretty Good Privacy](http://en.wikipedia.org/wiki/Pretty_Good_Privacy)

## About the Server Pool

This server is running a modified version of [sks-keyserver](https://bitbucket.org/skskeyserver/sks-keyserver/), version 1.1.5+, found on my private [git repository](http://code.mattrude.com/openpgp/sks-keyserver). The servers are physically located in New York City, New York, USA in the [DigitalOcean](https://www.digitalocean.com/?refcode=4b3eee7ba20c) NYC3 East Coast datacenter.

The main [hkp](http://tools.ietf.org/html/draft-shaw-openpgp-hkp-00) pool which you should configure your keyserver software to use is **pool.sks-keyservers.net**, or perhaps **subset.pool.sks-keyservers.net**. Folks will understand and expect that choice.

This server is running [SKS](https://bitbucket.org/skskeyserver/sks-keyserver/wiki/Home), the Synchronizing Key Server.

The available pools are listed below:

* **pool.sks-keyservers.net** - The main pool
* **p80.pool.sks-keyservers.net** - Servers with port 80 open for HKP access
* **subset.pool.sks-keyservers.net** - Servers that are running the lastest version of sks
* **ipv4.pool.sks-keyservers.net** - Servers with an IPv4 IP address
* **ipv6.pool.sks-keyservers.net** - Servers with an IPv6 IP address
* **na.pool.sks-keyservers.net** - Servers located in North America
* **eu.pool.sks-keyservers.net** - Servers located in Europe
* **oc.pool.sks-keyservers.net** - Servers located in Oceania

More information may be found on the SKS Keyservers [Pools Overview Page](https://sks-keyservers.net/overview-of-pools.php).

## About this Service

This service may be withdrawn at any time and without notice to end-users. (Peers will be notified). End-users should use a pool definition above, such as **pool.sks-keyservers.net** which will alias into an operational pool.

_**Note:** This service is provided free, to the public, in the hopes that it might prove useful. **No warranty is provided**, nor any offer of continuing service or access._

_By using this service, you **MUST** understand that presence of data in the keyserver (pools) in no way connotes trust. Anyone can generate a key, with any name or email address, and upload it. All security and trust comes from evaluating security at the "object level", via PGP [Web-Of-Trust](http://en.wikipedia.org/wiki/Web_of_trust) signatures. This keyserver makes it possible to retrieve keys, looking them up via various indices, but the collection of keys in this public pool is KNOWN to contain malicious and fraudulent keys. It is the common expectation of server operators that users understand this and use software which, like all known common OpenPGP implementations, evaluates trust accordingly. This expectation is so common that it is not normally explicitly stated._
