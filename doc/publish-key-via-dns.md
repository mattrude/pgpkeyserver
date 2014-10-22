---
layout: default
title: Publish A Public PGP Key via DNS
permalink: /guides/publish-key-via-dns/
---

A keyserver is not the only way to publish your public pgp key.

## Publishing A Public PGP Key via DNS

One method of publishing public pgp keys, other then on a keyserver, is to use a <abbr title="Dynamic Name Server">DNS</abbr> recorded with a <abbr title="Public Key Association">PKA</abbr> (Public Key Association) entry. PKA is a simple way of storing the look up infromation for your public key in a DNS TXT record.

### Creating the DNS Record

Start out by exporting your public key as an **ascii armored** (by using the **-a** flag) public key, to a file on your webserver.

    $ gpg -a --export 0x0A1B2C3E > /var/www/html/0x0A1B2C3E.asc

This key/file must be accessible via a public URL on the domain the email address is associated with. In this example, the file <code><strong>/var/www/html/0x0A1B2C3E.asc</strong></code> is able to be downloaded by going to <code><strong>http://example.com/0x0A1B2C3E.asc</strong></code>.

Next you need to get your key&#039;s fingerprint, to do so, list your public key with the <code><strong>--fingerprint</strong></code> flag.

<pre><code>$ gpg --fingerprint --list-keys 0x0A1B2C3E

pub   4096R/0A1B2C3E 2014-06-19 [expires: 2020-06-19]
      Key fingerprint = <strong>AA11 BB22 CC33 DD44 EE55  FF66 GG77 HH88 II99 JJ00</strong>
uid                  Example User <user@example.com></code></pre>

Using your <code><strong>Key fingerprint</strong></code>, remove the spaces and add the content to the below line, with the URL for the above key.

    v=pka1;fpr=AA11BB22CC33DD44EE55FF66GG77HH88II99JJ00;uri=http://example.com/0x0A1B2C3E.asc

And add this to your DNS server as a TXT field.  The pointer of the record should be the user section of your email address followed by _pka (ie: "user._pka.example.com". 

### Testing the PKA Record

There are two main parts to a PKA setup, the first being the DNS record, and the second is the actual key to be downloaded.

#### Testing the DNS Record

To test the DNS record, using `dig`, run the following command after changing **user** and **example.com** in the below example to your own **user** and **domain** part of your email address.

<pre><code>$ dig +short txt <strong>user</strong>._pka.<strong>example.com</strong></code></pre>

Should produce the same value as was entered into the DNS TXT record from above.

<pre><code>"v=pka1;fpr=AA11BB22CC33DD44EE55FF66GG77HH88II99JJ00;uri=http://example.com/0x0A1B2C3E.asc"</code></pre>

#### Testing the URL

Testing the url is simple, you can browse it it via your web client or you can download it via `wget` or `curl`

**wget** will download the file to your file system

    wget http://example.com/0x0A1B2C3E.asc

**curl** will display the content of the file on your screen

    curl http://example.com/0x0A1B2C3E.asc

### Importing a key via PKA

You may run the following command to import your key into your key ring. Just change user@example.com to the email address you wish to import.

<pre><code>$ gpg --auto-key-locate pka -ear <strong>user@example.com</strong></code></pre>

### Other PKA Resources

* More information may be found at [gushi.org](http://www.gushi.org/make-dns-cert/HOWTO.html) and [grepular.com](https://grepular.com/Publishing_PGP_Keys_in_the_DNS)
