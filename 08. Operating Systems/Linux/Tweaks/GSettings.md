# GSettings

GSettings — это конфиг для GTK-оболочки.

UI для редактирования
```shell
sudo apt install gnome-tweaks

gnome-tweaks
```


## Gnome settings

```shell
gsettings set org.gnome.desktop.interface text-scaling-factor 1.14  # Увеличить DPI, лучше чем scaling factor
gsettings set org.gnome.desktop.interface font-name 'Noto Sans 11' # выставить системный UI шрифт 
```

Настройки шрифтов:
```shell
gsettings get org.gnome.desktop.interface font-antialiasing
gsettings get org.gnome.desktop.interface font-hinting
```
