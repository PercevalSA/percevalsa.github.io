---
title: "HifiBerry OS configuration"
date: 2022-10-20
description: "How to build librespot for picore player"
image: https://www.hifiberry.com/wp-content/uploads/2020/01/filter.jpg
---

# HifiBerry OS Customisations

Before moving to my custom version of piCorePlayer with my own Spotify extension I used to run HifiBerry on my raspberryPi.
Here are some modification I made to improve performances.

### SSH for security
add my ssh key
```bash
mkdir .ssh
vi .ssh/authorized_keys
```
Configure your ssh server to allow ket authentication only. Refuse passwords auth!

### disable useless pieces

disable useless streaming services. I only need airport and spotify.
We need to keep `spotify` and `shairport-sync` and disable the following:
 * `raat`
 * `mpd`
 * `squeezelite`
 * `bluealsa-aplay`
 * `alsaloop`
 * `dlnampris`
 * `mopidy`
 * `upmpdcli`

disable useless services:
 * hifiberry data collector
 * roon
```bash
systemctl stop datacollector.service
systemctl disable datacollector.service
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

