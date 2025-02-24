---
title: "Open Windows URL shortcut in Linux"
date: 2025-02-24
description: "How to automatically open windows URL shortcut in your default browser on GNOME"
image: images/internet-explorer-11-logo.jpg
---

# Introduction

I am working on a robot project with my brother. We share data (datasheets, pictures...) in a folder synchronized on dropbox.
He only uses Windows and I am mainly using Linux (debian based distros with GNOME Desktop Environment to be precise).
He tend to use Windows URL file to save wbe shortcut in a sharable way. But those file are not openable by default in Linux.

**Here is a way to automatically open Microsoft Windows URL shortcut file in Linux default browser.**

## MS Windows URL shortcut

A url file has the `.url` extension and look like this
```ini
[InternetShortcut]
URL=https://www.example.com/
```

# Creating the right application

In order to automatically open `.url` files we need to create an "application" in GNOME. 
This will be a bash script and and `.desktop` file.
This will create an "open with" entry in the selection menu.

## create executable

Create the bash script in `/usr/bin/mswin-urlfile` and paste this content
```bash
#!/usr/bin/bash
if [ "$1" == "" ] || [ "$1" == "--help" ] ; then
  echo "$(basename "$0") URLFILE - opens a .url file in the default browser"
else
  sed 's/^BASEURL=/URL=/' "$1" | grep -m 1 '^URL=' | sed 's/^URL=//' | sed 's/\r//' | xargs xdg-open
fi
```

Then enable execution permission with 
```bash
sudo chmod +x /usr/bin/mswin-urlfile
```

## create application entry

To create an application entry in GNOME, we need to create a `.dekstop` file.
create `/usr/share/application/winurl.desktop` and paste this content
```ini
[Desktop Entry]
Type=Application
Name=WinURL
GenericName=WebBrowser
Icon=application-x-mswinurl
Comment="open msurl file in default browser"
Categories=Network;Utility;Viewer;WebBrowser
Keywords=web;url;windows;microsoft;win;ms;
NoDisplay=false

Exec=/usr/bin/mswin-urlfile %u
MimeType=application/x-mswinurl
```

## select our new "application" to open URL files

1. Find a `.url` file with the file explorer;
1. right click on it and select "open with";
1. scroll down the list and select "WinURL";
1. enable "Always use for this file type" at the bottom;
1. finally click on "open".

![open with menu](/images/open-msurl-with.png)

Tadam! You can now open all `.url` files in your default browser by double clicking on it.

# Sources

* [Open URL files in Linux](https://askubuntu.com/a/387741/1636114)
* [Creating .desktop files](https://www.reddit.com/r/linux4noobs/comments/114w615/tutorial_creating_quick_and_dirty_desktop_files/)
* [.desktop specifications](https://specifications.freedesktop.org/desktop-entry-spec/latest/recognized-keys.html)