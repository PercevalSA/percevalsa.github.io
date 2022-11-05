---
title: "HifiBerry OS configuration for streaming use only"
date: 2022-11-05
draft: true
---

# HifiBerry OS
My personnal configuration

add my ssh key
```sh
mkdir .ssh
vi .ssh/authorized_keys
```

players vailable: `SERVICES="spotify raat mpd shairport-sync squeezelite bluealsa-aplay alsaloop dlnampris mopidy upmpdcli"`


disable useless services:
 * hifiberry data collector
 * roon

```sh
systemctl stop datacollector.service
systemctl disable datacollector.service
systemctl stop raat
systemctl disable raat

```
I'm on RaspberryPi 2 so I don't use bluetooth or WiFi
```sh
systemctl stop wpa_supplicant
systemctl disable wpa_supplicant
systemctl stop wpa_supplicant@wlan0
systemctl disable wpa_supplicant@wlan0
systemctl stop bluealsa
systemctl disable bluealsa
systemctl stop mpris-proxy
systemctl disable mpris-proxy
```

