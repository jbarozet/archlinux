# Arch Installation Part4 (Gnome Customization)

<br>

## Windows

Gnome Tweak Tool alias Ajustements.
Windows Title Bars > Title Bars button (ON)


## Shortcuts

Settings > Keyboard shortcuts
=> Navigation > "Hide all normal windows" => assign SUPER (CMD on a Mac keyboard) + D


<br>


## Install browsers

```bash
# pacman -S firefox
# yay -S brave
```



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


download zip archive

Create extensions directory:
```bash
# mkdir ~/.local/share/gnome-shell/extensions
```

unzip in that folder:
```bash
# unzip dash-to-dockmicxgx.gmail.com.v68.shell-extensio,.zip ~/.local/share/gnome-shell/extensions
```

<br>


## Fonts

Luke Smith:
- ttl-linux-libertine
- Console: ttf-inconsolata

```bash
# pacman -S ttf-{bitstream-vera,liberation,freefont,dejavu} freetype2
```

```bash
# pacman -S ttl-linux-libertine ttf-inconsolata
```


<br>


## Themes

https://www.gnome-look.org

Install:
- For Themes: Download archives and extract. Copy files in ~/.themes/
- For icons: Download archives and extract. Copy files in ~/.icons/


### Enable gnome shell

By default, shell is disable in Tweaks > Appareance.

To enable gnome shell:
- Open Gnome Tweak Tool
- Click on the Extensions menu item and move the "User Theme" slider to "On"
- Close Gnome Tweak Tool
- You should now be able to choose a Shell Theme in Apparence Menu


### Themes and icons used by various distributions

Manjaro:
- Applications: Matcha-sea
- Cursor: Xcursor-breeze
- Icons: Papirus-Dark-Maia
- Shell: Matcha-sea

Ubuntu 20.1
- Applications: Yaru-Dark ? Yaru-Light
- Cursor: Yaru
- Icons: Yaru
- Sound: Yaru


### Install Matcha theme (Manjaro)

https://www.gnome-look.org/p/1187179/
- Matcha-aliz: red version
- Matcha-azul: blue version
- Matcha-sea: sea green version



### Ultimate Maia Theme (my preferred)

https://www.gnome-look.org/s/Gnome/p/1193879
Download, extract archive and move files to ~/.themes


### Ultimate Maia icons (my preferred)

Ultimate Maia icons:
https://www.gnome-look.org/s/Gnome/p/1218961
Download, extract archive and move files to ~/.icons


### Cursors

xcursor-breeze






