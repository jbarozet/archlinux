# Arch Installation Part4 (Gnome Customization)

<br>

## Windows

Gnome Tweak Tool alias Ajustements.
Windows Title Bars > Title Bars button (ON)


<br>


## Wallpapers

https://www.gnome-look.org


<br>


## Install dash-to-dock extension.

This extension moves the dash off the overview.
So quicker to access. Always there.

Install dash to dock:

```bash
# sudo pacman -S chrome-gnome-shell
```

Then go to:
https://extensions.gnome.org


<br>


## Fonts

```
pacman -S ttf-{bitstream-vera,liberation,freefont,dejavu} freetype2
```

Luke Smith:
- ttl-linux-libertine
- Console: ttf-inconsolata


<br>


## Themes

### Themes used by various distributions

Themes used in Manjaro:
- Applications: Matcha-sea
- Cursor: Xcursor-breeze
- Icons: Papirus-Dark-Maia
- Shell: Matcha-sea

Themes used in Ubuntu 20.1
- Applications: Yaru-Dark ? Yaru-Light
- Cursor: Yaru
- Icons: Yaru
- Sound: Yaru


### Matcha Icons

Install (Matcha GBTC Theme - Used in Manjaro, GTK3 theme):
- matcha-gtk-theme
- matcha-kde-wallpapers

https://www.gnome-look.org/p/1187179/
- Matcha-aliz: red version
- Matcha-azul: blue version
- Matcha-sea: sea green version

Installation:
```bash
# git clone https://github.com/vinceliuice/Matcha-gtk-theme
# cd Matcha-gtk-theme
# ./install.sh
```

This will copy files in ~/.themes/


### Papirus-Dark-Maia icons

Ultimate Maia icons:
https://www.gnome-look.org/s/Gnome/p/1218961
Download, extract archive and move files to ~/.icons



### Cursors

xcursor-breeze






