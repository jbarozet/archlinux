# Arch Installation Part2 (Finalize)

Once restarted, login as root.

NetworkManager is installed (or wicd), so networking should work fine.

Disable wpa_supplicant which will conflict with NetworkManager
```bash
# systemctl stop wpa_supplicant
# systemctl disable wpa_supplicant
```

<br>

## Wired connection

You should be good to go. Check that you have an IP Address with:
```bash
# ip addr
```

<br>

## Wireless connection

Check if WiFi radio status. This can be done by executing the command below;
```bash
# nmcli radio wifi
```

If the WiFi radio is disabled, then you can enable it by running the command below;
```bash
# nmcli radio wifi on
```

Check status of network interfaces
```bash
# nmcli dev status                 
```

Display the list of SSID
```bash
# nmcli dev wifi list
```

 If SSID not listed, rescan
```bash
# nmcli dev wifi rescan
```

Connect to WiFi
```bash
# nmcli dev wifi connect Livebox-B00A password 'mypassword'
```

Check Device status
```bash
# nmcli dev status
```

Check Connections (--active to only list active ones)
```bash
# nmcli con show                               
 NAME    UUID                                  TYPE             DEVICE 
 Livebox a1900bed-baa9-47a3-affb-b640d0effe5d  802-11-wireless  wlan0
```

<br>

## Add NTP and Cron

Adding NTP (Clock sync) and cronie (admin task automation)

```bash
# pacman -Syy ntp cronie
```

If we want log in clear, then update file /etc/systemd/journald.conf and proceed with the following modifications. Replace:

```
#ForwardToSyslog=no
```
with:
```
ForwardToSyslog=yes
```

<br>

## Fonts

```
pacman -S ttf-{bitstream-vera,liberation,freefont,dejavu} freetype2
```

Luke Smith:
- ttl-linux-libertine
- console: ttf-inconsolata
- 

<br>

## Create User

Create a user and add to wheel group. Add password.

```
# useradd -m -g wheel -c 'Jean-Marc Barozet' -s /bin/bash jmb 
# passwd jmb
```

<br>

## Modify SUDOERS

To give access to sudo command, change the sudoers using command visudo. Do not update directly /etc/sudoers.
```
EDITOR=vim visudo
```

<br>

## AUR

To manage AUR packages, install Trizen or Yay.

Install Trizen :
```bash
# sudo pacman -S git
# git clone https://aur.archlinux.org/trizen
# cd trizen
# makepkg -sri
```

Or install Yay:
```bash
# sudo pacman -S git
# git clone https://aur.archlinux.org/yay
# cd yay
# makepkg -sri
```

<br>

## pamac (pacman graphical interface)

To install Pamac:

```bash
# yay -S pamac-aur
```

It will ask for some inputs.  
- I selected 1.) pamac-aur
- I removed dependencies after install
- I selected None on the next prompt.

Note:
- Yaourt and Packer => AUR helpers
- Replaced par Yay - Yet Another Yaourt

<br>

## Enable Services

systemd being used, here is a list of services that may have to be activated:
- syslog
- cron
- NTP to synchronize clock from the network

```
systemctl enable cronie
systemctl enable ntpd
```

