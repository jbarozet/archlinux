# pacman


## -S 

Update database and upgrade
```bash
# sudo pacman -Syu
```

Mettre à jour - si pas fait depuis longtemps force checking with yy
```bash
# sudo pacman -Syyu
```

Search for everything related to emacs on the remote repository (-Qs for local repository)
```bash
# sudo pacman -Ss emacs
```

## -R

Remove a package (-R) and dependencies (s)
```bash
# sudo pacman -Rs emacs
```

Remove a package (-R), config files (n) and dependencies (s)
```bash
# sudo pacman -Rns emacs
```

## -Q

List all installed packages (including dependent packages)
```bash
# sudo pacman -Q
```

List all installed packages you explicitly installed
```bash
# sudo pacman -Qe
```

List all installed packages you explicitly installed. Option q gives only the name and not the number or anything else (q works for every command):
```bash
# sudo pacman -Qeq
```

List all installed packages (including dependent packages - from main repository
```bash
# sudo pacman -Qn
```

List all installed packages (including dependent packages - from AUR repository
```bash
# sudo pacman -Qm
```

List unneeded dependancies (orphans)
```bash
# sudo pacman -Qdt
```

Search for emacs in the local repository
```bash
# sudo pacman -Qs
```

Pacman config

/etc/pacman.conf
- Add: Color
- Add: CheckSpace (check disk space)
- Add: ILoveCandy (add pacman stuff when downloading)

/etc/pacman.d/mirrorlist
- Change your preferred mirror (put at the top)



