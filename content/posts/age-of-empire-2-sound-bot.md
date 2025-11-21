---
title: "Age of Empire 2 sound bax as Telegram Bot"
date: 2025-09-10
description: "I created a Telegram bot that sends you random Age of Empire 2 sounds and taunts"
image: https://shared.akamai.steamstatic.com/store_item_assets/steam/apps/813780/capsule_616x353.jpg
---


## Age of Empire 2 sound bot

I have created a [Telegram bot](https://t.me/age_of_empires_2_bot) that sends you random Age of Empire 2 sounds and taunts.

## Telegram Bot

I used [python-telegram-bot library](https://python-telegram-bot.org/) to create the bot. The bot responds to the `/start` command and sends a welcome message. When the user sends commands to receive a specific or a random sound or taunt from the game.

I used the file cache system from telegram that allows you to refer to files by their file ID after the first upload. The bot does not need to re-upload the same sound file multiple times, saving bandwidth and improving performance.

## Automation

A python package is automaticlly built and deployed on PyPI using GitHub Actions whenever I push a new tag to the repository.

I deployed the bot on a Oracle Cloud Free Tier server. I used [systemd](https://www.freedesktop.org/wiki/Software/systemd/) to manage the bot as a service, ensuring it starts on boot and restarts if it crashes. The bot handles cache in a persistent way, so the cache is not lost when the bot restarts.

The bot downloads the sound files from its own GitHub repository at startup if they are not already present on the server. It allows a lightweight deployment without the need to embed all sound files in the python package.

The code for the bot is available on [GitHub](https://github.com/PercevalSA/aoe2-telegram-bot).