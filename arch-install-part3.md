# Arch Installation Part3 (Gnome)



## Install Gnome (Desktop Environment)

```
# pacman -S gnome gnome-extra 
```

Notes:
- gnome-software is now part of gnome meta package



## Install GDM (Display Manager)

A display manager, or login manager, is typically a graphical user interface that is displayed at the end of the boot process in place of the default shell. There are various implementations of display managers, just as there are various types of window managers and desktop environments. There is usually a certain amount of customization and themeability available with each one.

```
# pacman -S gdm
```


## Exit and log as user jmb

```
# exit
```


## Start GDM (Display Manager)

Just start the Display Manager (do not enable for now)

```
# systemctl start gdm.service
```

If everything looks good, enable graphical login with the appropriate systemd service. 
For example, for GDM, enable gdm.

```
# systemctl enable gdm
```

Reboot and you should have the login window automatically started:
- Select user
- Click on Settings
- Then pick Gnome - this is Gnome with Wayland



## AUR

Installer Trizen ou Yay pour compléter Pacman.
On peut utiliser trizen (écrit en perl) ou Yay (écrit en Go) à la place du vieillissant yaourt.

Install Trizen :

```
sudo pacman -S git
git clone https://aur.archlinux.org/trizen
cd trizen
makepkg -sri
```

Or install Yay :

```
sudo pacman -S git
git clone https://aur.archlinux.org/yay
cd yay
makepkg -sri
```



## Add-ons

xsane (pour le scanner), mais aussi unoconv (pour l’aperçu des fichiers dans Gnome Documents).

```
sudo pacman -S xsane unoconv
```




