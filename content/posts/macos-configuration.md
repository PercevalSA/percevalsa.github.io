---
title: How to configure my macbook after a new install
date: 2022-11-05
description: "A list of command and configuration to have clean environment under macOS"
image: images/macos.jpeg
draft: true
---


Open a terminal to enter the following commands.

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

install needed package
```
brew cask install android-platform-tools
brew install python3
```
