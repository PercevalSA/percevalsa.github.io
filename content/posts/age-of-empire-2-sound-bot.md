---
title: "Age of Empires II Sound Box on Telegram"
date: 2025-09-10
description: "A Telegram bot that sends random Age of Empires II sounds and taunts."
image: https://shared.akamai.steamstatic.com/store_item_assets/steam/apps/813780/capsule_616x353.jpg
---


## Age of Empires II Sound Bot

I built a [Telegram bot](https://t.me/age_of_empires_2_bot) that sends random Age of Empires II sounds and taunts.

![Age of Empires II Sound Bot](/images/aoe2-telegram-bot.png)

## Telegram bot

The bot is implemented with the [python-telegram-bot library](https://python-telegram-bot.org/). It responds to the `/start` command with a welcome message and supports commands to request either a specific sound or a random sound/taunt from the game.

It uses Telegram's file caching system, which allows the bot to reference files by their file ID after the first upload. This avoids re-uploading the same sound files, saving bandwidth and improving performance.

## Automation and deployment

A Python package is automatically built and published to PyPI via GitHub Actions whenever I push a new tag to the repository.

I deploy the bot to an Oracle Cloud Free Tier instance and use systemd to run it as a service so it starts on boot and restarts after crashes. The bot persists its cache to disk so cached file IDs survive restarts.

At startup the bot downloads any missing sound files from its GitHub repository, enabling a lightweight deployment without embedding all sound files inside the Python package.

The source code is available on [GitHub](https://github.com/PercevalSA/aoe2-telegram-bot).