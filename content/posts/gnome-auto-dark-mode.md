---
title: "Auto switch between light and dark mode on GNOME"
date: 2025-07-03
description: "Auto switch between light and dark mode on GNOME based on night mode configuration using systemd service and timer."
image: https://i0.wp.com/www.omgubuntu.co.uk/wp-content/uploads/2021/10/dark-mode.jpg
---

I want my Fedora 42 (works with 41 I guess) to automatically switch to dark mode at night and light mode during the day. I'm already using the night mode to trigger the blue light filter at specific times.

Thanks to Adam I had a base to start with. He wrote a blog post on having the dark mode auto switch with hard coded times. [You can read it here](https://www.technowizardry.net/2024/06/auto-switch-between-light-and-dark-mode-on-gnome/)

This part of the code is what troubled me the most, 
```bash
currenttime=$(date +%H:%M)
if [[ "$currenttime" > "17:00" || "$currenttime" < "09:00" ]]; then
    set_theme dark
else
    set_theme light
fi
```

To get the night mode times I used `gsettings` tool. [source](https://askubuntu.com/questions/1088650/gnome-night-light-setting-from-the-command-line)
```bash
gsettings get org.gnome.settings-daemon.plugins.color night-light-schedule-from
gsettings get org.gnome.settings-daemon.plugins.color night-light-schedule-to
``` 

But the output is in decimal time format (e.g., 20.3 for 20:18). We need to convert the current time in HH:MM format to decimal format (and not the oher way around to avoid float errors). Here is a piece of code that does this conversion:

```bash
# convert from HH:MM format (20:18) to decimal format (20.3)
time_to_decimal() {
    local time_format="$1"
    
    if [[ ! $time_format =~ ^[0-9]{1,2}:[0-9]{2}$ ]]; then
        echo "Error: Invalid format. Use HH:MM (ex: 20:18)"
        return 1
    fi
    
    # split hours and minutes
    local hours=$(echo "$time_format" | cut -d':' -f1)
    local minutes=$(echo "$time_format" | cut -d':' -f2)
    
    # convert minutes in fraction 
    # 18 minutes = 18/60 = 0.3 hour
    local decimal_part=$(echo "scale=1; $minutes * 10 / 60" | bc)
    
    echo "$hours.$decimal_part"
}
```

And the full result is
```bash
#!/bin/bash
set_theme() {
    if [[ "$1" == "dark" ]]; then
        new_gtk_theme="prefer-dark"
    elif [[ "$1" == "light" ]]; then
        new_gtk_theme="prefer-light"
    else
        echo "[!] Unsupported theme: $1"
        return
    fi

    export DBUS_SESSION_BUS_ADDRESS=$DBUS_SESSION_BUS_ADDRESS
    echo "$DBUS_SESSION_BUS_ADDRESS"
    current_gtk_theme=$(gsettings get org.gnome.desktop.interface color-scheme)
    # echo "[.] Currently using ${current_gtk_theme}"
    if [[ "${current_gtk_theme}" == "'${new_gtk_theme}'" ]]; then
        echo "[i] Already using gtk '${new_gtk_theme}' theme"
    else
        echo "[-] Setting gtk theme to ${new_gtk_theme}"
        gsettings set org.gnome.desktop.interface color-scheme ${new_gtk_theme}
        echo "[✓] gtk theme changed to ${new_gtk_theme}"
    fi
}

# convert from HH:MM format (20:18) to decimal format (20.3)
time_to_decimal() {
    local time_format="$1"
    
    if [[ ! $time_format =~ ^[0-9]{1,2}:[0-9]{2}$ ]]; then
        echo "Error: Invalid format. Use HH:MM (ex: 20:18)"
        return 1
    fi
    
    # split hours and minutes
    local hours=$(echo "$time_format" | cut -d':' -f1)
    local minutes=$(echo "$time_format" | cut -d':' -f2)
    
    # convert minutes in fraction 
    # 18 minutes = 18/60 = 0.3 hour
    local decimal_part=$(echo "scale=1; $minutes * 10 / 60" | bc)
    
    echo "$hours.$decimal_part"
}

# If script run without argument
if [[ -z "$1" ]]; then
    currenttime=$(time_to_decimal $(date +%H:%M))
    starttime=$(gsettings get org.gnome.settings-daemon.plugins.color night-light-schedule-from)
    endtime=$(gsettings get org.gnome.settings-daemon.plugins.color night-light-schedule-to)

    if [[ "$currenttime" > "$starttime" && "$currenttime" < "$endtime" ]]; then
        set_theme dark
    else
        set_theme light
    fi
else
    set_theme $1
fi
```

Once the script is ready I wanted to modify the timer to avoid hardcoded times. My timer will run every hour but you can increase the frequency if you want. [source](https://unix.stackexchange.com/questions/704109/configure-systemd-timer-to-run-every-hour-after-first-run)

```ini
[Unit]
Description=Auto adjusts GNOME theme between dark and light to match the time

[Timer]
OnBootSec=1min
OnUnitActiveSec=1h

[Install]
WantedBy=timers.target
```

# Sources

Credits to [Technowizardry](https://www.technowizardry.net/2024/06/auto-switch-between-light-and-dark-mode-on-gnome/) for the initial idea and code.

EDIT: there is a GNOME extension that does this automatically: [Night Theme Switcher](https://extensions.gnome.org/extension/2236/night-theme-switcher/). I didn't know about it when I wrote this post.

## Comments

To give feedback, send an email to percevalsa [at] outlook dot com.