# Fontconfig

[MAN](https://manpages.ubuntu.com/manpages/trusty/man5/fonts-conf.5.html)

**Fontconfig** это система управления шрифтами Linux.
Она решает:
* какой шрифт выбрать для определенного семейства
* какие fallback использовать
* какие настройки рендеринга применить

**Настройки рендеринга:**

`antialiasing` - сглаживание шрифта. Со включенным шрифты становятся "полупрозрачными"

`hinting` - это подгонка шрифта под пиксельную сетку. Проблема: векторный шрифт может попадать между пикселями. Hinting корректирует форму символа.

`hintstyle` - это степень `hinting`. Опции:
* hintnone - как macOS
* hintslight - мягкий
* hintmedium - Ubuntu
* hintfull - Windows

`subpixel rendering`
LCD пиксель состоит из `rgb` субпикселей. Subpixel rendering использует их отдельно. Это увеличивает эффективное разрешение текста.
Расположение может быть RGB, BGR - если выбрать неправильный, то текст выглядит размытым.

```shell
mkdir -p ~/.config/fontconfig
nano ~/.config/fontconfig/fonts.conf
```

```xml
<?xml version='1.0'?>
<!DOCTYPE fontconfig SYSTEM 'fonts.dtd'>
<fontconfig>
 <match target="font">
  <edit name="antialias" mode="assign"><bool>true</bool></edit>
  <edit name="hinting" mode="assign"><bool>true</bool></edit>
  <edit name="hintstyle" mode="assign"><const>hintslight</const></edit>
  <edit name="rgba" mode="assign"><const>rgb</const></edit>
  <edit name="lcdfilter" mode="assign"><const>lcddefault</const></edit>
 </match>
</fontconfig>
```

Чтобы система «увидела» изменения в правилах и заново проиндексировала шрифты, выполни команду:
```shell
fc-cache -fv
```
* -f: (force) принудительно пересканировать всё.
* -v: (verbose) покажет процесс (полезно, чтобы убедиться, что твой домашний каталог просканирован).

## Команда fc-match

Команда `fc-match` выдает настройки шрифтов для:
* **Конкретный шрифт**: `Inter`, `Ubuntu`, `JetBrains Mono`
* **Generic-семейство**: `sans-serif`, `serif`, `monospace`.

По умолчанию `fc-match` выдает только одну строку: имя файла и семейство.
Флаг `-v` заставляет утилиту вывалить абсолютно все свойства, которые `Fontconfig` применил к этому шрифту, пройдясь по всем файлам `fonts.conf`.

```shell
fc-match "monospace"                                                    # выведет имя стандартного шрифта для кода в системе
fc-match -v "JetBrains Mono"                                            # выведет все настройки для конкретного шрифта
fc-match -v "Ubuntu" | grep "hintstyle"                                 # поиск по настройкам
fc-match -v "sans-serif" | grep -E "family|style|hinting|antialiasing"  # основные настройки
```



```xml
<?xml version='1.0'?>
<!DOCTYPE fontconfig SYSTEM 'fonts.dtd'>
<fontconfig>
    <alias>
        <family>sans-serif</family>
        <prefer>
            <family>Noto Sans</family>
        </prefer>
    </alias>

    <match target="pattern">
        <test name="family"><string>Arial</string></test>
        <edit name="family" mode="assign" binding="strong">
            <string>Noto Sans</string>
        </edit>
    </match>

    <match target="pattern">
        <test name="family"><string>Helvetica</string></test>
        <edit name="family" mode="assign" binding="strong">
            <string>Noto Sans</string>
        </edit>
    </match>
</fontconfig>



```



### Фикс фоллбека для emoji

~/.config/fontconfig/conf.d/50-emoji.conf
```xml
<fontconfig>
 <alias>
  <family>monospace</family>
  <prefer>
   <family>Noto Color Emoji</family>
  </prefer>
 </alias>
</fontconfig>
```
