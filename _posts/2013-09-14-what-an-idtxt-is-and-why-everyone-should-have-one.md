---
layout: post
title: "What an id.txt is and why everyone should have one"
description: ""
category: cryptography
tags: ["Crypto"]
---

One of the key elements in asymmetric cryptography is key verification. All
the clever maths involved in GPG or S/MIME won't protect you, if you don't
verify the keys you trust and sign.

This turns out to be a problem if you are unable to meet the person you're
talking to in person. In fact all cryptosystems have this bootstrapping
problem: If you want to exchange encrypted messages with someone, you have
to meet them in person.

So a lot of hackers take part in the PGP "Web of trust" where we not only
trust people but also publicise the fact that we trust them - and this is
a awfully nice idea because tha way, we can graph the whole network of PGP
users and look for the shortest route between two GPG users and assess if
we want to trust all of the hops to be benevolent, competent and uncompromised.

The thing is, we technically would have to do that for every key we own.
