---
title: How to configure my macbook after a new install
date: 2022-11-05
description: "A list of command and configuration to have clean environment under macOS"
image: images/macos.jpeg
---

Don't write `.DS_Store` file on removable devices: 
```
defaults write com.apple.desktopservices DSDontWriteNetworkStores -bool true\
defaults write com.apple.desktopservices DSDontWriteUSBStores -bool true\
```

Install custom host file to make internet safer
```
cp hosts hosts.bak
sudo cp hosts hosts.bak
sudo cp /Users/perceval/Workspace/hosts/alternates/fakenews-gambling-porn/hosts /etc/hosts
sudo dscacheutil -flushcache;sudo killall -HUP mDNSResponder
udo killall mDNSResponderHelper
sudo cp /etc/hosts.new /etc/hosts
sudo killall -HUP mDNSResponder
sudo killall mDNSResponderHelper
sudo dscacheutil -flushcache
sudo launchctl start mDNSResponder
head /etc/hosts
```

add a command line access to sublime text
```
echo "/Applications/Sublime\ Text.app/Contents/MacOS/Sublime\ Text &" > /usr/local/bin/subl
chmod u+x /usr/local/bin/subl
rm /usr/local/bin/subl
ln -s /Applications/Sublime\ Text.app/Contents/SharedSupport/bin/subl /usr/local/bin/subl
subl /installer.failurerequests
cd /usr/local
```
Now I'm using vscode which has the `code` command line starter. If it is not installed by default you can use the same trick.

install needed package with [Homebrew](https://brew.sh/). Especially Python to get a more up to date version.
```
brew cask install android-platform-tools
brew install python3
```

I follow most of the advices [here](https://github.com/drduh/macOS-Security-and-Privacy-Guide) to harden my configuration. Except from "phone home" stuff because I like imessage on my macbook.
