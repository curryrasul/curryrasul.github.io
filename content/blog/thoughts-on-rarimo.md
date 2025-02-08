+++
title = "My thoughts on Rarimo vs vOPRF/MPC-based approach"
date = 2025-02-07
draft = false

[taxonomies]
tags = ["cryptography", "zk-id"]

[extra]
lang = "en"
toc = true
comment = true
math = false
mermaid = false
outdate_alert = false
outdate_alert_days = 120
+++

*In this blog post I'm leaving my thoughts on [Rarimo's](https://docs.rarimo.com/) approach on solving ["Uniqueness, Anonymity, Eligibility - The Great Trilemma"](https://mirror.xyz/0x90699B5A52BccbdFe73d5c9F3d039a33fb2D1AF6/1JSk1fvRwpfUQhLZt1OlaBpVDkXIutVYYXdYgaukh0c) problem. It's recommended to read [Web2 Nullifiers using vOPRF](https://curryrasul.com/blog/web2-nullifiers-2/) for understanding.*

## Introduction

I wrote a few blog posts ([pt.1](https://curryrasul.com/blog/web2-nullifiers/), [pt.2](https://curryrasul.com/blog/web2-nullifiers-2/)) about a way to generate "nullifiers" for Web2-IDs. As I said, we can build many different apps/protocols with that, including those that use "zk passport".

Rarimo's [Freedom Tool](https://docs.rarimo.com/freedom-tool/) is an attempt to build an anonymous voting protocol, with proof of eligibility based on zk passport. In general, the protocol faces the same problem. They described it in their [blog post](https://mirror.xyz/0x90699B5A52BccbdFe73d5c9F3d039a33fb2D1AF6/1JSk1fvRwpfUQhLZt1OlaBpVDkXIutVYYXdYgaukh0c) as "The Great Trilemma" problem:

> The Great Trilemma:
> 
> - **Uniqueness**: Each user should have only one identity.
> - **Anonymity**: No one should be able to link a user to their identity.
> - **Eligibility**: Only those who can provide zk passport proof should be able to participate in the protocol.

The main point here is that: 
> Guaranteeing one or two of them is easy, but guaranteeing all three simultaneously is incredibly difficult

---

There's no ideal solution to that problem, but there can be attempts to solve it. I'm going to list a few:

1. Trusted ID provider (which runs in TEE) that generates "nullifiers" for you.
2. vOPRF-based approach. 
3. Rarimo's "Incognito Passport-based protocol" approach. 

I think it's clear that the first option is not a good solution. That's why I want to focus on comparison of 2nd and 3rd options and their problems.

---

### Rarimo's approach

*I'm not going to provide a detailed explanation of the protocol here, both because I don't know about it well enough and because I don't think it's necessary - you can read [Rarimo's blog post](https://mirror.xyz/0x90699B5A52BccbdFe73d5c9F3d039a33fb2D1AF6/1JSk1fvRwpfUQhLZt1OlaBpVDkXIutVYYXdYgaukh0c) for that.*

Rarimo's "Incognito Passport-based protocol" is a "generalized ID infrastructure that publicly associates the user’s passport with an identity key and then reuses this association for all required cases (voting and others). This means that instead of single use cases for country-specific passports there is a huge active network of users with different characteristics that different applications can interact with.

In short, it's a trustless registry stored on-chain, where people can register using their passport data.

How it works:

1. You create your key pair (sk, Pk)
2. You register your public key with zk passport proof attached and also hash(passport). You also commit to DG1 passport's data group, so that you can generate zk proofs about passport.
3. Now you can reuse your identity in different apps. nullifier = hash(sk, APP_ID)

## Problems & Comparison

Now that you know how both protocols work, let's compare them.

**The Main problem/disadvantage of Rarimo's approach** is that it relies on "privacy network effect" for anonymity (similar to Tornado Cash). What does that mean? Basically, it doesn't provide complete anonymity. During the registration process, you link hash(passport) with your identity. Governments can easily find out who's in the registry by just hashing all the passports they issued. 
I think that's a big problem:
* In case there's small amount of users based on certain eligibility criteria, it'll be very easy to deanonymize user and their action
* Even if we have big amount of users - governments can still track who's in the registry. If we talk about voting in a non-democratic country, the only way to make it safe is to register a really large percentage (maybe even the majority) of users in the registry. Imo, that's only possible if Rarimo collabs with the government for that; it's a good idea, but I don't think non-democratic regimes would agree to do that. It's possible to collab with democracies though, but I don't think we even need to host that kind of voting there, because it's already honest.

*If we have million citizens of X country in the registry, it'll be hard to track who made what action exactly due to privacy network effect. But I don't think it's a big problem for a non-democratic country, to persecute all those million people.*

**The Main problem/disadvantage of vOPRF/MPC-based approach** is that in case of collusion of MPC nodes, it'll be possible to deanonymize users completely.
Generally this scheme has many advantages as well, like:
* Provides good UX: Users don't need to go through any registration process. Instead, app creator can set the eligibility criteria, and people will be able to interact with the app right away.
* Completely private (in case of majority of MPC nodes are honest) - there's no way to find out that a Web2 identity used the protocol (even for MPC nodes, governments, id-providers, etc.)

## Conclusion

While it's hard for me to say which approach is objectively better, I believe that the vOPRF approach with MPC nodes (run in a TEE) distributed across the world would provide better privacy.

*A more important point is that we can try to create a better solution by combining both approaches. For example, what if we use Rarimo's approach for registration but, instead of revealing hash(passport), we reveal vOPRF(hash(passport))?
This would provide the same level of anonymity as the vOPRF/MPC-based approach, which, in my opinion, is more secure. Over time, we would also benefit from the privacy network effect similar to Rarimo.
Essentially, in the worst-case scenario — if the MPC nodes collude — we would still have the anonymity provided by Rarimo’s network effect.*

I'm interested in hearing your thoughts on this, so feel free to leave a comment or DM me on Telegram [@curryrasul](https://t.me/curryrasul).