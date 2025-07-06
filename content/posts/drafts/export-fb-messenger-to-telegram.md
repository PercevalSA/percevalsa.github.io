---
title: "Export facebook messenger conversation to Telegram"
date: 2025-06-30
description: "How to keep messages history with media when migrating from messenger to Telegram"
image: https://images.fastcompany.com/image/upload/f_webp,c_fit,w_750,q_auto/wp-cms/uploads/2018/02/p-1-apple-pulls-encrypted-messenger-app-telegram-from-the-app-store-1.jpg
draft: true
---

So you want to migrate from Facebook Messenger to Telegram, but you don't want to lose your conversation history, including media files? Here's how you can do it.

# requirements

You will need to export your Facebook Messenger messages using Facebook data export feature. Then you will have to replay all messages in a new conversation in Telegram using the Telegram API. 
For the sake of readability I will use both my account and my wife's account so each message will be sent by its original author instead of having all sent by a bot in the conversation.

I wanted to restore the conversation history in Telegram, so I needed to export the messages from Facebook Messenger. I used the [Facebook Messenger Exporter]()
