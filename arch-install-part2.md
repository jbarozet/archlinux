# Arch Installation Part2 (Finalize)

Once restarted, login as root.
NetworkManager is installed (or wicd), so networking should work fine.

Adding NTP (Clock sync) and cronie (admin task automation)

```
pacman -Syy ntp cronie
```

If we want log in clear, then update file /etc/systemd/journald.conf and proceed with the following modifications. Replace:

```
#ForwardToSyslog=no
```
with:
```
ForwardToSyslog=yes
```


## Video

Install drivers for video cards. xf86-video-vesa package includes a lot of drivers.
Check this for more information: https://wiki.archlinux.org/index.php/Xorg#Driver_installation

Nvidia 
- check wiki ttps://wiki.archlinux.org/index.php/NVIDIA. 
- ptimus : https://wiki.archlinux.org/index.php/NVIDIA_Optimus

VirtualBox
- In addition to xf86-video-vesa, install virtualbox-guest-utils

```
pacman -S xf86-video-vesa
pacman -S virtualbox-guest-utils
```

Enable kernel modules:
```
systemctl enable vboxservice
```


## Fonts

```
pacman -S ttf-{bitstream-vera,liberation,freefont,dejavu} freetype2
```

Luke Smith:
- ttl-linux-libertine
- console: ttf-inconsolata
- 

## Create User

Create a user and add to wheel group. Add password.

```
# useradd -m -g wheel -c 'Jean-Marc Barozet' -s /bin/bash jmb 
# passwd jmb
```


## Modify SUDOERS

To give access to sudo command, change the sudoers using command visudo. Do not update directly /etc/sudoers.
```
EDITOR=vim visudo
```



## Enable Services

systemd being used, here is a list of services that may have to be activated:
- syslog
- cron
- avahi, avahi-dnsconfd: CUPS
- org.cups.cupsd: printers
- bluetooth
- NTP to synchronize clock from the network

```
systemctl enable syslog-ng@default
systemctl enable cronie
systemctl enable avahi-daemon
systemctl enable avahi-dnsconfd
systemctl enable org.cups.cupsd
systemctl enable bluetooth
systemctl enable ntpd
```


