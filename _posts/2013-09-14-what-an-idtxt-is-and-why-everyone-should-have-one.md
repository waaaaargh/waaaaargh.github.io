---
layout: post
title: "What an id.txt is and why everyone should have one"
description: ""
category: Applied Cryptography
tags: ["Crypto"]
---

One of the key elements in asymmetric cryptography is key verification. All
the clever maths involved in GPG or S/MIME won't protect you, if you don't
verify the keys you trust and sign.

This turns out to be a problem if you are unable to meet the person you're
talking to in person. In fact all public-key-cryptosystems have this bootstrapping
problem: If you want to exchange encrypted messages with someone, you have
to meet them in person.

Most of the hackers I know already use GPG, and since GPG is one of the most
common and proven cryptography tools out there, we might as well reduce all
our verification to GPG.

And this is where the idea of the id.txt comes in. It's simple. We write up
all our cryptographic information, like OTR keys and key signing policy in a
single text file and clear-sign it with gpg. we then publish it for others to
download and verify.

Here's some stuff that should go in your `id.txt`:
* the date you created it
* the canonical location where you publish it
* your OTR Keys by for every account and resource you control.
* fingerprints of (self-signed) SSL certificates you control.

The last step is to `gpg --clearsign` it and push it to your webserver.

Now, what did we gain? For example, if I want to contact you - let's say via
XMPP or any other protocol that OTR has love for - I don't need to verify your
OTR fingerprint by phone or meeting in person. If I already have signed your
GPG key or have a business card with your GPG key fingerprint on it handy, I
can quickly verify yout OTR key fingerprint and we can communicate via
encrypted chat.

One step to streamline this would be the logical next-step: take the id.txt
and turn it into an `id.xml` so that it becomes not only human- but
machine-readable and we can write tools that automatically parse it and take
the tasks of comparing long hexadecimal numbers off our hands.
