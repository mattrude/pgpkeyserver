---
layout: default
title: Extracting
permalink: /doc/extracting/
---

## How-to extract a key

1. Type the text you want to search for in the **Search String** box. If you want to look up a key by its hexadecimal KeyID, you have to prefix the ID with 0x
2. Select either the **Index** or **Verbose Index** check box. The **Verbose** option will display signatures on keys.
3. Press the **Submit** button.

The server will return a list of keys matching the search text. The page will have links for every KeyID, and every bracket-delimited identifier (i.e. &lt;user@example.com&gt;). Clicking on the hypertext link will display an ASCII-armored version of the public key.

## Extract a key from the pool

<p>You can extract a key by typing in some words that appear in the user id of the key you are looking for, or by typing in the keyid in hex format (“0x…”)</p>

{% include form-extract.html %}

## Extraction caveats
The search engine is not the same as that used by the pgp program. It will return information for all keys which contain all the words in the search string. A "word" in this context is a string of consecutive alphabetic characters. For example, in the string user@example.com, the words are **user**, **example**, and **com**.

This means that some keys you might not expect will be returned. If there was a key in the database for Marc Edu <mit.foo.com>, this would be returned for by the above query. If you do not want to see all these extra matches, you can select "Only return exact matches", and only keys containing the specified search string will be returned.

This algorithm does not match partial words in any case. So, if you are used to specifying only part of a long name, this will no longer work.

Currently, hypertext links are only generated for the KeyID and for text found between matching brackets. (It is a common convention to put your e-mail address inside brackets somewhere in the key ID string.)

