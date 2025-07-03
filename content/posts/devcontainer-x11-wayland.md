---
title: "Run a X11 app in a devcontainer on wayland only distro"
date: 2022-11-05
description: "I run fedora which is a wayland only distro. I need to run a X11 app in a devcontainer."
image: https://avatars.githubusercontent.com/u/14821372?v=4
---


# Introduction

I'm running fedora after running Ubuntu for a long time. For a project I need to run a X11 application in a devcontainer.
This devcontainer needs an access to the `Xauthority` file in order to display items on the screen.
On ubuntu, and `.Xauthority` file is automatically created by the gnome session in the home folder of the user.
But on fedora, the `.Xauthority` file is created in `/run/user/1000/` and not in the home folder.
This is a problem because the devcontainer is not able to access this file.

My mount configuration in the devcontainer settings: 
```json
"mounts": [
    "source=${localEnv:HOME}${localEnv:USERPROFILE}/.ssh,target=/home/vscode/.ssh,type=bind,readonly,consistency=cached",
    "source=${localEnv:HOME}/.Xauthority,target=/home/vscode/.Xauthority,type=bind,readonly,consistency=cached",
    "source=/tmp/.X11-unix,target=/tmp/.X11-unix,type=bind,consistency=cached",
    "source=/dev/,target=/dev/,type=bind,consistency=consistent"
],
"runArgs": [
    "--privileged",
    "--env",
    "DISPLAY"
],
```

> ℹ️ **`HOME` vs `USERPROFILE`**
> <br>I'm working with people using windows computers. The dual notation `USERPROFILE` and `HOME` allows the mount to work on both Windows and Linux hosts.
{.note}

# Solution

Create a systemd service that will create the `.Xauthority` file in the home folder of the user. So the devcontainer can access it. It will be a symlink to the file created by the gnome session.

`/etc/systemd/user/xauthority.service`
```ini
[Unit]
Description=Create Xauthority to forward screen in devcontainers

[Service]
Type=oneshot
#User=perceval
#Group=perceval
WorkingDirectory=/home/perceval/
ExecStartPre=/usr/bin/rm -rf /home/perceval/.Xauthority
ExecStart=/usr/bin/bash -c "while [ ! -e /run/user/1000/.mutter-Xwaylandauth.* ]; do sleep 1; done; ln -s /run/user/1000/.mutter-Xwaylandauth.* .Xauthority"

[Install]
WantedBy=graphical-session.target
```

## Comments

To give feedback, send an email to percevalsa [at] outlook dot com.