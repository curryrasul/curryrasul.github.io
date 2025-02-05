+++
title = "Gossip - Telegram bot for anon messaging using ZK"
date = 2025-01-24
draft = false

[taxonomies]
tags = ["software", "zk"]

[extra]
lang = "en"
toc = true
comment = true
math = false
mermaid = false
outdate_alert = false
outdate_alert_days = 120
+++

*Thanks for help, [Ramil](https://t.me/gaframil) and [Aleksandr](https://t.me/Lezheboker)*

# Gossip - Telegram bot for anon messaging using ZK

## What's that?

Small group chats lack option to post something anonymously. There are few tries to build such bots - but they are not doing it with zk. In their case - their server knows everything. 
What we can do is to build a zk chat bot, where users will be able to post in a chat from the "name" of the bot. Users will only need to prove they are part of this group chat, without revealing who they are exactly.
Additionaly, we can go further and improve the system by adding rate-limiting. That's what Gossip is.

## Details

### How it works / UI

1. Admin adds bot to the group chat
2. Users can tap the link to open the webapp where they can send messages
3. In the webapp (client-side), RLN/Semaphore "account" is generated, using rlnjs
4. Now users can prove that they are part of a group without revealing who they are using zk and send a message with the proof
5. Bot verifies the proof and post the message

### Tech stack

* RLN [zk circuits](https://github.com/Rate-Limiting-Nullifier/circom-rln) - RLN circuits are needed for proving membership in group chats; + additionally it gives us rate-limiting/spam-prevention. You can read more about RLN [here](https://rate-limiting-nullifier.github.io/rln-docs/) 
* Telegram miniapp/webapp/bot
* [rlnjs](https://github.com/Rate-Limiting-Nullifier/rlnjs) on a client side for proof gen
* Groups management using [Bandada](https://github.com/bandada-infra/bandada)

This setup is already good, but we can make the system more secure by running the bot in TEE. That can help us to fight centralization/censorship. For example, we can use [Lit Protocol](https://developer.litprotocol.com/what-is-lit) for that.
Also, to zk prove membership in merkle tree (group), users have to know merkle proof itself, but they don't know. If a user requests merkle path for their merkle proof - we'll be able to deanonymyze the user. 
For now the solution is TEE as well. But in future, we'll improve this to merkle path retrieval with PIR (Private Info Retrieval).

## Motivation

I noticed such bot in the ZuConnect Community Hub telegram chat; that's basically the chat for "Zu" (zalu, connect) events. There was a group chat, where people were able to use ZuRat bot to post anonymously, by proving their [ZuPass](https://zupass.org/). 

That bot had few problems:

* it wasn't really anonymous - users needed to press the bot button; the problem is that this button sends logs from client side
* centralized/trusted

But the main problem is that it only worked with your ZuPass-ID, and it was generally made for this specific chat. 
Yes, I understand that it was just cool experiment. But why should we stop on that?
