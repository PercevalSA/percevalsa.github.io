---
title: "Flicker free splash screen on Boot with Yocto Linux"
date: 2024-01-20
description: "Get a nice splash screen with a logo while booting without any flash, flicker or black screen"
image: https://cdn.download.ams.birds.cornell.edu/api/v1/asset/623044671/1200
draft: true
---

# Introduction

I'm responsible of two yocto distributions. One for Nvidia Jetsons running headless and one for a x86 based touch screen.
I wanted the screen to have a nice boot animation without any flicker or black or white screen.
I successfully achieved that thanks to multiple tricks and pieces of knowledge that I wrote then here.

## Context

I'm based on `dunfell` branch for my Yocto distros. The boot process is based on UEFI (no more BIOS legacy stuff), Grub2, then Linux kernel with initramfs and then full GNU/Linux distro running from `/`.

I tried to use psplash but it is cumbersome and animations are not that pretty. I decided to use plymouth as it is the default splash screen in all major distros (ubuntu, fedora...).

