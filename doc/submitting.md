---
layout: default
title: Submitting Keys
permalink: /doc/submitting/
---

## How-to submit a key

1. Cut-and-paste an ASCII-armored version of your public key into the text box.
1. Press **Submit key to pool**.

That is it! The keyserver will process your request immediately. If you like, you can check that your key exists using the [extract]({{ site.url }}/doc/extracting/) procedure. 

## Submit a key to the pool

<p>Enter ASCII-armored PGP key here:</p>

{% include form-submit.html %}

**Notice:** When you submit a key to this server via that web interface or via HKP, that key will be migrated around the pool to the diffrent servers in the pool.  That key will then be stored on each of those servers in addition to this server.
