---
title: "Age of Empire 2 sound bax as Telegram Bot"
date: 2025-09-10
description: "I created a Telegram bot that sends you random Age of Empire 2 sounds and taunts"
image: https://shared.akamai.steamstatic.com/store_item_assets/steam/apps/813780/capsule_616x353.jpg
draft: true
---


## Age of Empire 2 sound bot

I have created a [Telegram bot](https://t.me/age_of_empires_2_bot) that sends you random Age of Empire 2 sounds and taunts.

## Telegram Bot

I used [python-telegram-bot library](https://python-telegram-bot.org/) to create the bot. The bot responds to the `/start` command and sends a welcome message. When the user sends commands to receive a specific or a random sound or taunt from the game.

I used the file cache system from telegram that allows you to refer to files by their file ID after the first upload. The bot does not need to re-upload the same sound file multiple times, saving bandwidth and improving performance.
