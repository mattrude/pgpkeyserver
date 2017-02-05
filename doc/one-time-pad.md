---
layout: default
title: Practical uses for a One-Time Pad
permalink: /guides/one-time-pad/
description: Password-Spliter - Practical uses for a One-Time Pad
tags: otp
redirect_from:
  - /otp/
---

In cryptography, the one-time pad (OTP) is a type of encryption which has been proven to be impossible to crack if used correctly. Each bit or character from the plaintext is encrypted by a modular addition with a bit or character from a secret random key (or pad) of the same length as the plaintext, resulting in a ciphertext. If the key is truly random, as large as or greater than the plaintext, never reused in whole or part, and kept secret, the ciphertext will be impossible to decrypt or break without knowing the key.  It has also been proven that any cipher with the perfect secrecy property must use keys with effectively the same requirements as OTP keys. However, practical problems have prevented one-time pads from being widely used.

#### Password Splitting

Using methodological taken from the one-time pad schema  it is possible to store a password with multiple individuals or parts.  All parts or shares of the encrypted password must be combined before the final password may be reviled.  This method allows for a password to be split into 2 or as many shares as an admin wishes. **You should download and open these documents in Adobe Reader, Chrome & Firefox do not display the sheets correctly.**

* [Secure Password Splitter & Convert Table Worksheet for 4 users]({{ site.url }}/doc/Secure_Password_Splitter__Convert_Table.pdf) pdf
* [Secure Password Splitter & Convert Table Worksheet for 2 users]({{ site.url }}/doc/Secure_Password_Splitter__Convert_Table-for2.pdf) pdf

Using the methods described in the above worksheets, you can actually encrypt any information as long as the key is equal or greater length then the message.

### References

* [http://www.pro-technix.com/information/crypto/pages/vernam_base.html](http://www.pro-technix.com/information/crypto/pages/vernam_base.html)
* [http://www.cryptomuseum.com/crypto/otp.htm](http://www.cryptomuseum.com/crypto/otp.htm)
* [http://users.telenet.be/d.rijmenants/en/onetimepad.htm](http://users.telenet.be/d.rijmenants/en/onetimepad.htm)

