---
title: "Yocto build on external SSD in devcontainers"
date: 2022-11-05
description: "How to build a yocto distro in a devcontainer with all files stored on an external SSD"
image: https://www.linuxfoundation.org/hs-fs/hubfs/yocto%20(1).png
---

I manage and build multiple yocto distributions at work. The [performances guidelines](https://wiki.yoctoproject.org/wiki/Build_Performance) advise to use another disk than the one the system is running on. As I have a laptop I only have one internal SSD. Therefore I need to use an external SSD to store my yocto files (downloads in `DL_DIR` , build in `TMPDIR` and cache in `SSTATE_DIR`).
It is a good idea to do so for three reasons :
 * First, the cache is enormous and storing it on another disk will spare a lot of needed space (moreover if your SSD is full your system takes more time to boot :s);
 * Second, as the files are regularly written, it extends my internal SSD lifespan;
 * Third and last it improves overall system performances as you are not writing to your main disk which do not slow downs other I/O of your running OS. 

for the context I using [VSCode](https://code.visualstudio.com/) on Ubuntu latest LTS. I build my distros in a devcontainer.
We need to access the external SSD in the devcontainer. 

We could directly mount the external disk in the devcontainer. For that we would add a `mount` instruction in the configuration file but it couldn't start the devcontainer if the SSD is not plugged in. Because the devcontainer would try to mount a missing folder.

To avoid that issue I decided to mount the `media` folder in the dev container. It allows to access all external storage from the devcontainer and does not prevent to open the project if nothing is plugged in or mounted. 

On Ubuntu the external storage are mounted in `/media/<user_name>/<device_name>` where `user_name` is the current user.
If you work with other people on the project, note that the username of the host will change. Therefore you cannot have a generic definition for your different paths configuration for `DL_DIR`, `TMPDIR` and `SSTATE_DIR` in your `local.conf`

We can have a generic solution by directly mounting `/media/<username>` from host to `/media` in devcontainer thanks to the `$HOST-USER` variable available for devcontainer configuration. The only caveat is that all developers need to use the same name for their external SSD storing yocto builds. It can be only the name of the partition. In my case I decided that the partition or the disk would need to be called `yocto`. As a result I will have an external SSD mounted automatically on my host Ubuntu as `/media/perceval/yocto` and then mounted as `/media/vscode/yocto` in my devcontainer (`vscode` is the name of the default user in a devcontainer).

Here is the corresponding line in my `.devcontainer` file
```json
"mounts": [
  "source=/media/$HOST-USER/,target=/media/$USER/,type=bind,consistency=cached"
]
```

## Comments

To give feedback, send an email to percevalsa [at] outlook dot com.