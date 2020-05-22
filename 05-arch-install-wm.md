# Arch Installation Part5 (Window Managers)


## Dwm Graphical Interface from Luke Smith

Just run the following as the root user:
```
# curl -LO larbs.xyz/larbs.sh >> larbs.sh
# sh labrs.sh
```



## Graphical Environment

Multiple choices
- Desktop Environment - Like Gnome, KDE - includes all packages and programs
- Window Manager - basic environment to manage window. But then you have to add dock, applets, etc

To have a graphical login, you will need a Display Manager (or Login Manager)
- Display Manager (Or login manager) - without that, you have to login and end up in a tty.




## Install gdm (Display Manager)

A display manager, or login manager, is typically a graphical user interface that is displayed at the end of the boot process in place of the default shell. There are various implementations of display managers, just as there are various types of window managers and desktop environments. There is usually a certain amount of customization and themeability available with each one.

To enable graphical login, enable the appropriate systemd service. 
For example, for GDM, enable gdm.service.

```
# systemctl enable gdm.service
```

Reboot and you should have the login window:
- Select user
- Click on Settings
- Then pick Gnome - this is Gnome with Wayland

WM Theme: Matcha-sea
- Theme: Match-sea
- Icons: Papirus-Dark-Maia



## Graphical Environment - Window Manager


To have a graphical environment, you need X.org
```
# pacman -S xorg-server xorg-init 
```

You can start X by running:
```
# startx 
```
This will read your $HOME/.xinitrc to know what to start

Let’s install a WM:
```
# pacman -S i3-gaps i3status rxvt-unicode dmenu 
```

Things you might need to install:
- Network Manager (nm-applet)
- Fonts

Install fonts
```
# pacman -S noto-fonts 
```

Make the X server to start i3 when it starts:
In $HOME/.xinitrc , put:
```
exec i3 
```

Type in ```# startx``` to start the X server





## Desktop installation from Luke Smith

Luke Smith 
- https://larbs.xyz/
- https://lukesmith.xyz/programs.html


To install:
```
# curl -L0 larbs.xyz/larbs.sh >> larbs.sh
# bash larbs.sh
```
