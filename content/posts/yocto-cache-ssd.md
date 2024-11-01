---
title: "Yocto build on external SSD in devcontainers"
date: 2022-11-05
description: "How to build a yocto distro in a devcontainer with all files stored on an external SSD"
image: https://www.linuxfoundation.org/hs-fs/hubfs/yocto%20(1).png
draft: true
---

In my day to day work I build multiple yocto distribution. The [performances guidelines](https://wiki.yoctoproject.org/wiki/Build_Performance) advise to use another disk than the one the system is running on. As I have a laptop I only have one internal SSD. Therefore I need to use an external SSD to store my yocto files.
I need to do that for two reasons. First the cache is enormous and storing it on another disk will spare a lot of needed space, internal SSD lifespan and overall system performances (if your SSD is full your system takes more time to boot :s).

for the context I using [VSCode](https://code.visualstudio.com/) on Ubuntu latest LTS. I build my distro in a devcontainer.

We need to mount the SSD in the devcontainer. We could mount the disk in the devcontainer. But the issue is we cannot start the devcontainer if the SSD is not plugged in. We need to mount the `media` folder in the dev container. But the username might change. We end up with this instructions in the `.devcontainer` file
```json
"mounts": [
  "source=/media/$HOST-USER/,target=/media/$USER/,type=bind,consistency=cached"
]
```
THe only issue is: we need to know the name of the disk to set it in the `local.conf` file. 