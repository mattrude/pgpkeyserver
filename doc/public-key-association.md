---
layout: default
title: OpenPGP Public Key Association (PKA)
permalink: /guides/public-key-association/
description: Public Key Association (PKA) allows you to publish your OpenPGP key to your DNS record
tags: pka, Public Key Association, pgp, gpg, GnuPG, DNS
redirect_from:
  - /public-key-association/
  - /pka/
---

## Publishing A Public PGP Key via DNS: <small>Public Key Association</small>

A keyserver is not the only way to publish your public pgp key.

One method of publishing public pgp keys, other then on a keyserver, is to use a <abbr title="Dynamic Name Server">DNS</abbr> record with a <abbr title="Public Key Association">PKA</abbr> (Public Key Association) entry. PKA is a simple way of storing the look up information for your public key in a DNS `TXT` record. PKA allows a user to send encrypted data to a email address by query the DNS server, caching the key&#039;s fingerprint, downloading the public key from the provided URL and validating the key using the cached fingerprint.

## Creating the DNS Record

Start out by exporting your public key as an **ascii armored** (by using the `-a` flag) public key, to a file on your webserver.

<pre>$ gpg -a --export 0xDD23BF73 > /var/www/html/keys/0xDD23BF73.asc</pre>

This key/file must be accessible via a public URL on the domain the email address is associated with. In this example, the file `/var/www/html/keys/0xDD23BF73.asc` is able to be downloaded by going to `http://mattrude.com/keys/0xDD23BF73.asc`.

Next you need to get your key&#039;s fingerprint, to do so, list your public key with the `--fingerprint` flag.

<pre>$ gpg --fingerprint --list-keys 0xDD23BF73

pub   4096R/DD23BF73 2014-06-19 [expires: 2020-06-19]
      Key fingerprint = <strong>AE73 8427 2B91 AD63 5902  320B 2714 3AFF DD23 BF73</strong>
uid                  Matt Rude <matt@mattrude.com></pre>

Using your `Key fingerprint`, remove the spaces and add the content to the below line, with the URL for the above key.

<pre>v=pka1;fpr=AE7384272B91AD635902320B27143AFFDD23BF73;uri=http://mattrude.com/keys/0xDD23BF73.asc</pre>

And add this to your DNS server as a `TXT` field.  The pointer of the record should be the user section of your email address followed by `_pka` (ie: `matt._pka.mattrude.com`).

The full BIND DNS entry would be

    matt._pka.mattrude.com. 86400   IN      TXT     "v=pka1\;fpr=AE7384272B91AD635902320B27143AFFDD23BF73\;uri=http://mattrude.com/keys/0xDD23BF73.asc"

## Testing the PKA Record

There are two main parts to a PKA setup, the first being the DNS record, and the second is the actual key to be downloaded.

### Testing the DNS Record

To test the DNS record, using `dig`, run the following command after changing **matt** and **mattrude.com** in the below example to your own **user** and **domain** part of your email address.

<pre>$ dig +short txt <strong>matt</strong>._pka.<strong>mattrude.com</strong></pre>

Should produce the same value as was entered into the DNS TXT record from above.

<pre>"v=pka1\;fpr=AE7384272B91AD635902320B27143AFFDD23BF73\;uri=http://mattrude.com/keys/0xDD23BF73.asc"</pre>

### Testing the URL

Testing the url is simple, you can browse it it via your web client or you can download it via `curl`.

<pre>$ curl http://mattrude.com/keys/0xDD23BF73.asc</pre>

Should produce the public key in the `0xDD23BF73.asc` file you created above.

<pre>
-----BEGIN PGP PUBLIC KEY BLOCK-----

mQINBFOk0+QBEADL9L1wjHGrBLWFxpZHumJJo+pjfLh7wtiOBqWv3aOyxOmyUzYo
h50IVaa3xjv6XiXYInh+gkrSdRq/jm0QHLqNNj0SGiabttaHDFiOsc2NBn+eu7ay
yYpXYPtzQmUrdSbi5UguiJguOrHKokyqj72HAwLQ1HxEZhcS7ZFijbYvOOmKL0DR
HOpM0O+ZElJE85Bl+Kv90WOq779dTBZTvmiB0dHuZRw/N0w30rjqkYhyEwxAlI8n
NfHwcH1+V+kFKVXOsbSXp4tW7i6Tm5BGuyj8imLcoYgL3r5QWzZcVq/xiTrDYHrg
T0OIGqpFL+nwGa+XEtBr7RD/Xzqx3Y3P7zKS+SxWoWITqFQOn13264wYBXj7ohjg
QWmpoQ9Rk8YIQBjEsBcznCQfB1ndDx06zueytMCgJjX/lm5VDjOToiBDok7h4Stc
4OeDXzItIHC5GnpREjkutN7GhIPcnBPgiKu6xc4eTfPyF8MSYxQtm3NWHT5Sx78+
kecX1YqMffOWkmGgx9Fwb1VmEflShODR0oe8VifHEv/dR/afroOSlH5iyUkQMNrU
f3aSsmWSU5FqPaxMzraJJrra+DMpO627FSglyYUH8S/tlgD9l9yX3JxRkwL19LMO
1aPUn72jlJcmbFLjgW8vEOlz15zcTajaxXDFEtVuDBKaOy5Oug8ee2uYzwARAQAB
tB1NYXR0IFJ1ZGUgPG1hdHRAbWF0dHJ1ZGUuY29tPokCPwQTAQIAKQUCU6TT5AIb
LwUJA8H7rAcLCQgHAwIBBhUIAgkKCwQWAgMBAh4BAheAAAoJECcUOv/dI79zj+cQ
AMZBjZA8LQmoPxlAbrrCcSi8hhlU2AS1Vr4VLMjTxcALHkVcfAPf/NP4xkTssLfT
6gcxyJUI+pY9x0e7a1tGYCVNjpTEhCgZWSNh+JN9Mzx3rHVeH/8TqInggydVu0dW
9KSrbhXtjarczh9BTx9yzPkA4Sl2DIpVZOYhkZYH7w9RuplfkGEvo3NRqmUX7TSe
2eo1yPNa4Yh3hfUv0K7C3TpNrZuTPc/pJwR9guOn/vl6XIabFbRfePyBX87+TiIO
BLX+AMWHklArI5azoCy8Rp30ztpAtUDqg8gXfrYz65AgerwprxGEvNhvv1Aa2Pqc
2lPPE7Gvr7CuPjUoCatKPikLGDwbP6fp3hjU1rnabEdlSg3FMa60Jk2zRNN2KusR
/MKvsnrY80w9E7miRY/lbkrqYQj40UdnI4GaWReSHQJFHRa0ecD65djodc2wID+p
qpEEbPP2HU2L9iU2KmG4DnH+VHJN9LPeUdWrDs23e5l/y+Fkd87lHQe/xGv1jry0
tOWI6YGvKZTSboJ7IA6ROH5+SLkrH/C4PeTiPPqNRerJZercaVxCnqcfDj8GZ6CC
Zw4sePv6GR0uhc/2vBryexqOHCN0bGMXz1aQcGbbIPqRC8Yxk/YpQ94bzkqDzsz+
KfA0ddb9tp4DHuHxO1QtnPDVY2TqBa9/fPjH3NSLegry
=oKC/
-----END PGP PUBLIC KEY BLOCK-----
</pre>

## Importing a key via PKA

You may run the following command to import your key into your key ring. Just change **matt@mattrude.com** to the email address you wish to import.

<pre>$ echo "Test message" |gpg --auto-key-locate pka -ear <strong>matt@mattrude.com</strong></pre>

The command should produce the following output, note the line "automatically retrieved 'matt@mattrude.com' via **PKA**".

<pre>gpg: requesting key DD23BF73 from http server mattrude.com
gpg: key DD23BF73: public key "Matt Rude <matt@mattrude.com>" imported
gpg: Total number processed: 1
gpg:               imported: 1  (RSA: 1)
gpg: <strong>automatically retrieved 'matt@mattrude.com' via PKA</strong>
gpg: DD23BF73: There is no assurance this key belongs to the named user

pub  4096R/DD23BF73 2014-06-21 Matt Rude <matt@mattrude.com>
 Primary key fingerprint: AE73 8427 2B91 AD63 5902  320B 2714 3AFF DD23 BF73

It is NOT certain that the key belongs to the person named
in the user ID.  If you *really* know what you are doing,
you may answer the next question with yes.

Use this key anyway? (y/N) <strong>y</strong></pre>

## Other PKA Resources

* More information may be found at [gushi.org](http://www.gushi.org/make-dns-cert/HOWTO.html), [df7cb.de](https://www.df7cb.de/blog/2007/openpgp-dns.html), [initd.net](http://www.initd.net/2010/12/adding-gpg-public-keys-to-your-dns.html), and [grepular.com](https://grepular.com/Publishing_PGP_Keys_in_the_DNS)
