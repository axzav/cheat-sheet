# Keyboard layouts

**Файлы всех лейаутов доступных в системе:**
```sh
cd /usr/share/X11/xkb/symbols/
```

**Настройки лейаутов, доступных в системе находятся в этих файлах:**
```sh
/usr/share/X11/xkb/rules/evdev.xml # Современные GUI-интерфейсы (GNOME, KDE, XFCE). Если раскладки нет здесь — она не появится в "Настройках".
/usr/share/X11/xkb/rules/evdev.lst # Утилиты командной строки (например, setxkbmap).
/usr/share/X11/xkb/rules/xorg.lst # старый стандарт, почти не используется
```

**Сброс кэша XKB:**
```sh
sudo rm -rf /var/lib/xkb/*.xkm #clear cache
```

**Протестировать раскладку, применит сразу в активной сессии до момента перелогина:**
```sh
setxkbmap -layout ru -variant unipunct # установит одну активную раскладку ru
setxkbmap -layout us,ru -variant ,unipunct # одна стандартная en, другая ru unipunct, "," значит что в en нет варианта
setxkbmap -layout us,ru # без варианта, стандартная раскладка
```

**После обновления файлов настроек лэйаутов нужно перелогиниться в систему**

---

## Unipunct

Альтернативная раскладка, которая оставляет все символы латинской раскладки на тех же местах и перемещает некоторые редкие символы (ю, э, щ, ё, ц, й) на слой `altgr`

```sh
// ru(unipunct):
// `~ 1! 2@ 3#3№ 4$ 5% 6^ 7& 8* 9( 0) -_ =+
// хХ бБ уУюЮ кК еЕэЭ нН гГ шШщЩ жЖ зЗ [{ ]} \|
// фФ ыЫ вВ аА пП рР оОёЁ лЛ дД ;: '"           //"
// яЯ чЧцЦ сС мМ иИйЙ тТ ьЬъЪ ,< .> /?
```

В ubuntu чтобы включить нужно:

### 1. Пофиксить лейаут
```c
xkb_symbols "unipunct" {
    ...

    include "level3(ralt_switch)" // добавить отсутствущий слой altgr
};
```

### 2. Добавить в список лейаутов

```c
// /usr/share/X11/xkb/rules/evdev.lst
! variant
  unipunct        ru: Russian (unipunct)
```


```xml
<!--  /usr/share/X11/xkb/rules/evdev.xml -->
    <layout>
      <configItem>

        ...

        <name>ru</name>

        ...

      </configItem>
      <variantList>

        ...

        <variant>
          <configItem>
            <name>unipunct</name>
            <description>Russian (unipunct)</description>
          </configItem>
        </variant>

        ...

    </layout>
```

### 3. Включить altgr для us раскладки (для предотвращения горячих клавиш)

```с
default partial alphanumeric_keys modifier_keys
xkb_symbols "basic" {

    ...

    include "level3(ralt_switch)"
};

```
