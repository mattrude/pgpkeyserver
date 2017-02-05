---
layout: default
title: Frequently Asked Questions
permalink: /doc/faq/
---

Below you will find questions pertaining to this server (and perhaps other sks-keyservers).

**Can you delete my key from the key server?**

* No, we cannot remove your key from the key server. When you submit a key to our key server the key is also forwarded to other key servers around the world, and they in turn forward the key to still other servers. Deleting the key from our server would not cause it to be deleted from any of the other servers in the world and so this is not an effective way to ensure the discontinued use of your key.

**So, you can't delete my key, is there anything I can do?**

* If you still have the private key, you can use your PGP software to generate a revocation certificate, and upload that to the keyserver. The exact procedure for generating a revocation certificate varies depending on what PGP software you are using, please consult the documentation for more information. This will not delete your key from the key server, but it will tell people who download it that the key has been revoked, and should not be used.

**But the reason I want to delete my key from the keyserver is that I lost the private key (so I can't generate a revocation certificate), can you please delete my key for me?**

* No.  Due to the keyserver's peering service, any key deleted on one server will be readded by another server. So even if I were to delete your key from my database, it would be readded a few minutes latter from another keyserver.

**Can I delete old email addresses / IDs from my key on the keyserver?**

* No, For the same reasons you can't delete a key.

**I'm having trouble submitting my key with a photo to the keyserver, any idea what the problem is?**

* This problem should now be fixed.

**I think spammers got my email address from the PGP keyserver. What can I do?**

* Yes, there have been reports of spammers harvesting addresses from PGP keyservers. Unfortunately, there is not much that either we or you can do about this. Our best suggestion is you take advantage of any spam filtering technology offered by your ISP.

**I get a "Malformed Key" error when trying to upload a revocation certificate. Why doesn't this work?**

* Try applying the revocation certificate to your local key-ring and then re-uploading your key to the keyserver.

**I can't look up keys on the keyserver, and may be seeing the error "connection timed out." However, I can read this FAQ.**

* The usual cause for this is that your machine is behind a firewall that is blocking traffic (on port 11371) to the keyserver, and are using an older cached URL. Please remove the ":11371" from the URL and try again.
