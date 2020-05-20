# Arch Installation Part4 (Gnome Customization)




## Windows

On va personnaliser le bureau Gnome en lui ajoutant la date complète et les boutons pour minimiser et maximiser les fenêtres avec Gnome Tweak Tool alias Ajustements.

Windows Title Bars > Title Bars button (ON)



## Wallpapers

https://www.gnome-look.org



## Install dash-to-dock extension.

This extension moves the dash of the overview.
So quicker to access. Always there.

Install dash to dock:

```
# sudo pacman -S chrome-gnome-shell
```

Then go to:
https://extensions.gnome.org




## Fonts

```
pacman -S ttf-{bitstream-vera,liberation,freefont,dejavu} freetype2
```

Luke Smith:
- ttl-linux-libertine
- Console: ttf-inconsolata




## Themes

### Manjaro Themes

Themes used in Manjaro:
- Applications: Matcha-sea
- Cursor: Xcursor-breeze
- Icons: Papirus-Dark-Maia
- Shell: Matcha-sea

matcha-gtk-theme
matcha-kde-wallpapers

Matcha GBTC Theme (Used in Manjaro, GTK3 theme)
https://www.gnome-look.org/p/1187179/
- Matcha-aliz: red version
- Matcha-azul: blue version
- Matcha-sea: sea green version

Installation:
```
# git clone https://github.com/vinceliuice/Matcha-gtk-theme
# cd Matcha-gtk-theme
# ./install.sh
```

This will copy files in ~/.themes/


### Ubuntu 20.1 Themes

Themes:
- Applications: Yaru-Dark ? Yaru-Light
- Cursor: Yaru
- Icons: Yaru
- Sound: Yaru




### Cursors

xcursor-breeze



### Icons

Papirus-Dark-Maia

Ultimate Maia icons:
https://www.gnome-look.org/s/Gnome/p/1218961
Download, extract archive and move files to ~/.icons





