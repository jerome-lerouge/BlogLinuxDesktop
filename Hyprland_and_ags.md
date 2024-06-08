# Installing Hyprland and ags  

## Hyprland

### Manage wallpapers
Install hyprpaper

`$ pacman -S hyprpaper` 

Download your wallpapers inside a directory
Use a script to unload and load the specifics wallpapers inside the directory
Add a keystroke in the hyprland.conf to execute the script

### Manage lock screen

Install hyrplock

`$ pacman -S hyprlock` 

### Manage idle

Install hypridle

`$ pacman -S hypridle` 
Configure hypridle with ~/.config/hypr/hypridle.conf

## ags

### Exemples

Source de la documentation [Wiki AGS](https://aylur.github.io/ags-docs/config/examples/) 

Need to install adw-gtk-theme and to configure new theme to replace the current 'Adwaita'.

```bash
paru -S adw-gtk-theme
gsettings set org.gnome.desktop.interface gtk-theme 'adw-gtk3' && gsettings set org.gnome.desktop.interface color-scheme 'default'
paru -S upower # Package dependencies for AGS Service Battery 
systemctl enable upower.service
```

### Configuration GTK3

Documentation GTK-3 settings on Wayland [Documentation](https://github.com/swaywm/sway/wiki/GTK-3-settings-on-Wayland) 

```bash
$ gsettings list-keys org.gnome.desktop.interface 
```


