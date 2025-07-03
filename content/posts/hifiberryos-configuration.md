---
title: "HifiBerry OS configuration"
date: 2022-10-20
description: "How to build librespot for picore player"
image: https://www.hifiberry.com/wp-content/uploads/2020/01/filter.jpg
---

# HifiBerry OS Customizations

Before moving to my custom version of piCorePlayer with my own Spotify extension I used to run HifiBerry on my raspberryPi.
Here are some modifications I made to improve performances.

### SSH for security
I always harden all my Linux boxes. [more on how to improve ssh security](https://www.digitalocean.com/community/tutorials/how-to-harden-openssh-on-ubuntu-20-04). Configure your ssh server to [allow key authentication only](https://www.digitalocean.com/community/tutorials/how-to-harden-openssh-on-ubuntu-20-04#step-4-advanced-hardening).

Add my ssh key to to authorized key for easier remote control
```bash
mkdir .ssh
vi .ssh/authorized_keys
```

### disable useless running processes

To improve performances on the RaspberryPi I disable all audio services I am not using and all extra processes not useful to me.
I only need **apple airplay** and **spotify** there I only need to keep `shairport-sync` and `spotify`.
I disable the following services:
 * `raat`
 * `mpd`
 * `squeezelite`
 * `bluealsa-aplay`
 * `alsaloop`
 * `dlnampris`
 * `mopidy`
 * `upmpdcli`

I disable useless services:
 * hifiberry data collector;
```bash
systemctl stop datacollector.service
systemctl disable datacollector.service
```
 * [roon](https://roon.app/fr/) for local music is which I have not.
```bash
systemctl stop raat
systemctl disable raat
```

I'm on RaspberryPi 2 so I don't use bluetooth or WiFi
```bash
systemctl stop wpa_supplicant
systemctl disable wpa_supplicant
systemctl stop wpa_supplicant@wlan0
systemctl disable wpa_supplicant@wlan0
systemctl stop bluealsa
systemctl disable bluealsa
systemctl stop mpris-proxy
systemctl disable mpris-proxy
```

After a reboot and we are good to go
```bash
sudo reboot
```

## Comments

To give feedback, send an email to percevalsa [at] outlook dot com.