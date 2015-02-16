---
layout: default
title: About
permalink: /about/
---

## What is PGP?

PGP or Pretty Good Privacy is a way of encrypting and signing a email message in a "_end to end_" way.  This means, the message is encrypted on your computer in a way that the e-mail server has no knowledge of the message or the key that was used to encrypt the message.  The recipient of the message then decrypts the message on their own computer.

## About the Server Pool

The main [hkp](http://tools.ietf.org/html/draft-shaw-openpgp-hkp-00) pool which you should configure your keyserver software to use is **pool.sks-keyservers.net**, or perhaps **ha.pool.sks-keyservers.net**. Folks will understand and expect that choice. (Also **na.pool.sks-keyservers.net** in North America or **eu.pool.sks-keyservers.net** in Europe).

This server is running [SKS](https://bitbucket.org/skskeyserver/sks-keyserver/wiki/Home), the Synchronizing Key Server.

## About this Server

This service may be withdrawn at any time and without notice to end-users. (Peers will be notified). End-users should use a pool definition below, such as **pool.sks-keyservers.net** which will alias into an operational pool.

* **pool.sks-keyservers.net** - The main pool
* **p80.pool.sks-keyservers.net** - Servers with port 80 open for HKP access
* **ipv4.pool.sks-keyservers.net** - Servers with an IPv4 IP address
* **ipv6.pool.sks-keyservers.net** - Servers with an IPv6 IP address
* **na.pool.sks-keyservers.net** - Servers located in North Aamerica
* **eu.pool.sks-keyservers.net** - Servers located in Europe
* **oc.pool.sks-keyservers.net** - Servers located in Oceania

More infromation may be found on the SKS Keyservers [Pools Overview Page](https://sks-keyservers.net/overview-of-pools.php).

_**Note:** This service is provided free, to the public, in the hopes that it might prove useful. **No warranty is provided**, nor any offer of continuing service or access._

_By using this service, you **MUST** understand that presence of data in the keyserver (pools) in no way connotes trust. Anyone can generate a key, with any name or email address, and upload it. All security and trust comes from evaluating security at the "object level", via PGP [Web-Of-Trust](http://en.wikipedia.org/wiki/Web_of_trust) signatures. This keyserver makes it possible to retrieve keys, looking them up via various indices, but the collection of keys in this public pool is KNOWN to contain malicious and fraudulent keys. It is the common expectation of server operators that users understand this and use software which, like all known common OpenPGP implementations, evaluates trust accordingly. This expectation is so common that it is not normally explicitly stated._
