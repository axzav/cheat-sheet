# Layout experiment

https://github.com/urob/zmk-config#timeless-homerow-mods


Home row mods требуют правильных таймингов.
В config желательно настроить:

```shell
CONFIG_ZMK_HOLD_TAP=y
CONFIG_ZMK_HOLD_TAP_TERM_MS=200
CONFIG_ZMK_HOLD_TAP_INTERRUPT_FLAVOR=y
```

```shell
CONFIG_ZMK_HOLD_TAP=y
CONFIG_ZMK_HOLD_TAP_TERM_MS=180
CONFIG_ZMK_HOLD_TAP_FLAVOR="balanced"
```

В keymap editor можно включить "balanced" flavor.

----

Я бы начал с максимально комфортных, не агрессивных:

CONFIG_ZMK_HOLD_TAP=y
CONFIG_ZMK_HOLD_TAP_TERM_MS=200
CONFIG_ZMK_HOLD_TAP_FLAVOR="balanced"
CONFIG_ZMK_HOLD_TAP_REQUIRE_PRIOR_IDLE_MS=100

Почему так:

200ms — достаточно щадящий порог
balanced — универсальный
require_prior_idle — снижает случайные моды в словах

Если почувствуешь, что:
моды срабатывают слишком поздно → уменьшаем до 180
случайно ловишь Shift → уменьшаем до 170
сложно вызвать мод → увеличиваем до 220

## BASE_MAC

Левый большой:
tap → Enter
hold → SYM

[ Enter ]   [ SYM ]   [ TAB ]

tap → Enter
hold → SYM

tap → Backspace
hold → NUM

tap → Tab
hold → SYS

----

[ Space ]   [ NAV ]   [ ESC ]

Правый большой палец:
tap → Space
hold → NAV

&lt NAV SPACE

tap → Space
hold → NAV

tap → Delete
hold → NUM

tap → ESC
hold → SYS


---

Home Row Mods
```shell
A → Ctrl
S → Option
D → Cmd
F → Shift
```

&mt LCTRL A
&mt LALT  S
&mt LGUI  D
&mt LSHIFT F

## BASE_WINLIN 




## NAV (&mo NAV)

```shell
H   J   K   L
←   ↓   ↑   →
```

```shell
U   I   O   P
Home End PgUp PgDn
```



H  J  K  L   →  ←  ↓  ↑  -> самый частый
/ I  J  K  L
/ U  H  J  K



## SYM

под левой рукой

!  @  #  $  %  ^
&  *  (  )  _  +
{  }  [  ]  |  \
<  >  ?  :  ;  "


## NUM

под правой рукой

7 8 9
4 5 6
1 2 3
0 . =


## SYS

&to 0  (mac)
&to 1  (win/linux)
