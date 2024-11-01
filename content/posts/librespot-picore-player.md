---
title: "librespot on piCorePlayer"
date: 2022-11-05
description: "How to build librespot for piCorePlayer"
image: images/picore-logo.jpeg
---

# Introduction

All the code is available in [my github repo](https://github.com/PercevalSA/pcp-librespot) 

I wanted a stable headless Spotify player for my living room. It has to run on a RaspberryPi and boot fast in order to turn it on only when needed with a switch.
One of the solution would be Volumio or HifiBerry but they take too much time to boot. [piCorePlayer](https://www.picoreplayer.org/) is a good alternative but it has too much stuff installed by default and the Spotify extension is based on an outdated extension. I would use something based on [piCore](https://iotbyhvm.ooo/picore-tiny-core-linux-on-raspberry-pi/) which is the port of [Tiny Core Linux](http://www.tinycorelinux.net/) to the RaspberryPi. piCorePlayer is based on piCore. 
To stream spotify we will use [librespot](https://github.com/librespot-org/librespot). There is no librespote package for Tiny Core Linux and therefore for piCore. Therefore we need to build our own Spotify package. The purpose is to gain stability and performance compared to the piCorePlayer version. 
For the OS we have two solutions: 
1. use piCorePlayer and remove everything that is not needed and install only my Spotify module;
2. start from a bare piCore image. 

I finally started from piCorePLayer because it configures my DAC out of the box. In the future I would migrate to a bare piCore version.

## Requirements

To install something on TinyCore Linux we need to build a `tcz` extension. a `tcz`extension is then mounted at boot as `squashfs`. This allows to fast boot. 

Sources:
- [librespot](https://github.com/librespot-org/librespot)
- [PiCore Player](https://www.picoreplayer.org) 


# Build

Librespot must be cross-compiled and placed in the `sbin` folder as `librespot`. Don't forget to `chmod +x librespot` before creating the `tcz` extension.

### Cross Compilation
Cross compilation of Librespot for RaspberryPi on Linux (raspbian, TinyCoreLinux, PiCore Linux...)
You must be on Debian 11 minimum for the cross compilation to work(because of libs version dependencies)

### Install cross-compilation tools

```bash
sudo apt install git curl pkg-config build-essential gcc-arm-linux-gnueabihf lib32stdc++6 libstdc++6:armhf
```

### Install RUST
```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source $HOME/.cargo/env
```

### Install Libraries

Create a folder where you will work.
```bash
cd 
mkdir -p pcp-librespot/downloads/
cd pcp-librespot/downloads
```

We need to download arm `gnueabihf` version of needed libraries. We search for armhf versions in the repos. Download only the lib you need for your  backend:
 * alsa and alsa dev for alsa backend
 * portaudio and portaudio dev for portaudio backend
 * libjack and libjack dev for jack backend (default in librespot)

| Lib             | Source                                                   | Name of package                                                                    |
| --------------- | -------------------------------------------------------- | ---------------------------------------------------------------------------------- |
| portaudio19     | http://ftp.fr.debian.org/debian/pool/main/p/portaudio19/ | libportaudio2_19.6.0-1.1_armhf.deb                                                 |
| portaudio19 dev | http://ftp.fr.debian.org/debian/pool/main/p/portaudio19/ | portaudio19-dev_19.6.0-1+deb10u1_armhf.deb or portaudio19-dev_19.6.0-1.1_armhf.deb |
| alsa            | http://ftp.fr.debian.org/debian/pool/main/a/alsa-lib/    | libasound2_1.2.4-1.1_armhf.deb                                                     |
| alsa dev        | http://ftp.fr.debian.org/debian/pool/main/a/alsa-lib/    | libasound2-dev_1.2.4-1.1_armhf.deb                                                 |
| libjack         | http://ftp.fr.debian.org/debian/pool/main/j/jackd2/      | libjack-jackd2-0_1.9.17~dfsg-1_armhf.deb                                           |
| libjack dev     | http://ftp.fr.debian.org/debian/pool/main/j/jackd2/      | libjack-jackd2-dev_1.9.17~dfsg-1_armhf.deb                                         |

We also need the armhf libc 
```bash
wget ftp.fr.debian.org/debian/pool/main/g/gcc-10/libstdc++6_10.2.1-6_armhf.deb
```


### environment

If you still in `downloads` folder go back with `cd ..` else `cd pcp-librespot`
We need to configure the environement with a `sysroot` where we put all the libs needed for cross-compilation as librespot for arm can't use x86 libs.
```bash
mkdir sysroot
```
Go where you've download the libs and decompress packages in `pcp-librespot/sysroot` folder.
```bash
for a in $(ls $HOME/pcp-librespot/downloads);do ar p $a data.tar.xz | tar -xv -J -C $HOME/pcp-librespot/sysroot;done
```

### build
get the librespot sources
```bash
git clone https://github.com/librespot-org/librespot.git
```

Configure rust for cross-compilation
```bash
echo "arm-linux-gnueabihf-gcc --sysroot $HOME/pcp-librespot/sysroot \"\$@\"" >> ./cargo/bin/gcc-sysroot
chmod +x .cargo/bin/gcc-sysroot 

echo <EOF>> $HOME/.cargo/config
[build]
# Set default build target to armv7hf
target = "armv7-unknown-linux-gnueabihf"
# Configure linker for your target
[target.armv7-unknown-linux-gnueabihf]
linker = "gcc-sysroot"
EOF

export PKG_CONFIG_ALLOW_CROSS=1
export PKG_CONFIG_SYSROOT_DIR=$HOME/pcp-librespot/sysroot/
export PKG_CONFIG_DIR
export PKG_CONFIG_PATH=$HOME/pcp-librespot/sysroot/usr/lib/arm-linux-gnueabihf/pkgconfig/
```

configure librespot build for cross-compilation
```bash
cd librespot
rustup target add armv7-unknown-linux-gnueabihf
rustup component add rustfmt
```

to build librespot with alsa-backend; this is what you want for raspbian and piCore Player
```bash
cargo build --target=armv7-unknown-linux-gnueabihf --no-default-features --features "alsa-backend"
```

if you need portaudio backend do this instead
```bash
cargo build --target=armv7-unknown-linux-gnueabihf --no-default-features --features "portaudio-backend"
```

you might need to specify  `-L "/usr/lib/arm-linux-gnueabihf"` for building.


Create a folder to build the package and copy the binary into the package folder
```bash
cd $HOME/pcp-librespot
mkdir -p package/usr/local/sbin/
cp target/armv7-unknown-linux-gnueabihf/release/librespot package/usr/local/sbin/
chmod +x package/usr/local/sbin/librespot
```
Now we can buid a Tiny Core package: an extension.

## create extension to be installed

create the extension
```bash
mksquashfs package pcp-librespot.tcz
```

# Installation

### Install librespot extension on pcp

send it on your RaspberryPi running piCorePlayer:
```bash
scp pcp-librespot.tcz tc@pcp.local:
ssh tc@pcp.local
mv -v pcp-librespot.tcz /etc/sysconfig/tcedir/optional
```
activate start up on boot
```bash
echo pcp-librespot.tcz >> /etc/sysconfig/tcedir/onboot.lst
echo "/usr/local/etc/init.d/librespot start" >> /opt/bootlocal.sh 
pcp bu
```

reboot or launch the extention with
```bash
tce-load -i pcp-librespot
```

And that's it. A fast booting Linux running Librespot to stream Spotify on your speaker.