---
title: "Librespot Picore Player"
date: 2022-11-05T20:14:03+01:00
description: 'As soon as Winston had dealt with each of the messages, he clipped his speakwritten corrections to the appropriate copy of the Times and pushed them into the pneumatic tube. '
image: images/cctv.jpeg
draft: true
---

# Librespot for piCore Player

This is a tcz extension to install on piCore Player.
The purpose is to gain stability and performance compared to the LMS version.

## installation

create the extension
```bash
mksquashfs pcp-librespot pcp-librespot.tcz
```
put it on pcp
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
## creation

Librespot must be cross-compiled and placed in the `sbin` folder as `librespot`. Don't forget to `chmod +x librespot` before creating the tcz extension.

### Cross Compilation
Cross compilation of Librespot for RaspberryPi on Linux (raspbian, TinyCoreLinux, PiCore Linux...)
You must be on Debian 11 minimum for the cross compilation to work(because au libs version)

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
We need to download arm gnueabihf version of needed libraries. We search for armhf versions in the repos. Download only the lib you need for your  backend:
 * alsa and alsa dev for alsa backend
 * portaudio and portaudio dev for portaudio backend
 * libjack and libjack dev for jack backend (default in librespot)

| Lib | Source | Name of package |
|-----|--------|-----------------|
| portaudio19 | http://ftp.fr.debian.org/debian/pool/main/p/portaudio19/ | libportaudio2_19.6.0-1.1_armhf.deb |
| portaudio19 dev | http://ftp.fr.debian.org/debian/pool/main/p/portaudio19/ | portaudio19-dev_19.6.0-1+deb10u1_armhf.deb or portaudio19-dev_19.6.0-1.1_armhf.deb |
| alsa| http://ftp.fr.debian.org/debian/pool/main/a/alsa-lib/ | libasound2_1.2.4-1.1_armhf.deb |
| alsa dev |  http://ftp.fr.debian.org/debian/pool/main/a/alsa-lib/ | libasound2-dev_1.2.4-1.1_armhf.deb |
| libjack | http://ftp.fr.debian.org/debian/pool/main/j/jackd2/ | libjack-jackd2-0_1.9.17~dfsg-1_armhf.deb |
| libjack dev | http://ftp.fr.debian.org/debian/pool/main/j/jackd2/ | libjack-jackd2-dev_1.9.17~dfsg-1_armhf.deb |

We also need the armhf libc 
```bash
wget ftp.fr.debian.org/debian/pool/main/g/gcc-10/libstdc++6_10.2.1-6_armhf.deb
```

We need to configure the environement with a sysroot where we put all the libs needed for cross-compilation as librespot for arm can't use x86 libs.
```bash
cd librespot
mkdir sysroot
```
Go where you've download the libs and decompress packages in `librespot/sysroot` folder.
```bash
for a in $(ls);do ar p $a data.tar.xz | tar -xv -J -C $HOME/pcp-librespot/librespot/sysroot;done
```

### build
get the librespot sources
```bash
git submodule update --init --depth 1
```
or
```bash
git clone https://github.com/librespot-org/librespot.git
```

Configure rust for cross-compilation
```bash
echo "arm-linux-gnueabihf-gcc --sysroot $HOME/sysroot \"\$@\"" >> ./cargo/bin/gcc-sysroot
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
export PKG_CONFIG_SYSROOT_DIR=$HOME/librespot/sysroot/
export PKG_CONFIG_DIR
export PKG_CONFIG_PATH=$HOME/librespot/sysroot/usr/lib/arm-linux-gnueabihf/pkgconfig/
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


copy the binary into the package
```bash
cp target/armv7-unknown-linux-gnueabihf/release/librespot ../pcp-librespot/usr/local/sbin/
chmod +x ../pcp-librespot/usr/local/sbin/librespot
```
you can create the extension go to the begining of the readme

