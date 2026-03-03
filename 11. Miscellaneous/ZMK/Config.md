# Config

**Thumb cluster** - кнопки под большим пальцем
**QWERTY, Colemak, Dvorak** - популярные раскладки
**Home Row Modifiers** - Кнопки ASDF на Base Layer при зажатии работают как ⌃ ⌥ ⌘ ⇧
**Hyper Key** - это виртуальный модификатор, который одновременно нажимает ⌃ ⌥ ⌘ ⇧, редко используется поэтому можно настроить разные действия на Hyper Key + что угодно
**Tri-layer** - Зажимаются 2 клавиши слоев для активации 3го

## Стандартные слои

| Слой                  | Что включает                       |
| --------------------- | ---------------------------------- |
| 🧱 **Base Layer**     | Общая раскладка (QWERTY / Colemak) |
| 🛠 **Function Layer** | F1–F12, media, стрелки             |
| 🔠 **Symbols**        | ! @ # $ % и т. д.                  |
| ⌨ **Navigation**      | стрелки, PgUp, Home/End            |


## Поведение

| Поведение | Что делает                        |
| --------- | --------------------------------- |
| `&mo 1`   | Включает слой 1, пока удерживаешь |
| `&tog 1`  | Переключает слой (вкл/выкл)       |
| `&to 1`   | Делает слой 1 активным базовым    |


&mt MOD KEY
tap → печатает букву
hold → работает как модификатор

&mt LGUI A
нажал быстро → a
удержал → Cmd

&tap_dance - двойной тап

https://zmk.dev/docs/keymaps/behaviors/hold-tap?examples=home_row_mods

Home row mods


## Modifiers handling

Here are some different styles for handling modifiers on smaller keyboards:
- home row mods – ex. [Miryoku](https://github.com/manna-harbour/miryoku)
-- [Precondition’s guide](https://precondition.github.io/home-row-mods) is the de facto bible for tuning
- one shot mods – ex. [callum](https://github.com/callum-oakley/qmk_firmware/tree/master/users/callum), [seniply](https://stevep99.github.io/seniply/)
- combo mods – ex. [rafaelromao](https://github.com/rafaelromao/keyboards), [Jason Cox](https://jasoncarloscox.com/writing/combo-mods/)

There are a lot of different ways to handle home row mods. 
Is your slowness due to waiting for a timeout? Because there are several versions that don't involve waiting such as urob's [timeless modifiers](https://github.com/urob/zmk-config#timeless-homerow-mods) or if you're on zmk, the [balanced flavor](https://zmk.dev/docs/keymaps/behaviors/hold-tap) of hold taps will trigger as long as the interrupting key is fully contained within the modifier key.

Also consider if you have specific modifiers you use a lot, you could make dedicated keys for those on different layers. Like if there are only a handful of alt or gui that you use, you may not even need the general alt/gui key and can just set keys on layers to do the few keys you need. Or just do that with the frequently used ones.



https://mark.stosberg.com/markstos-corne-3x5-1-keyboard-layout/
https://keymapdb.com/?keyCount=38-42&isSplit=true&languages=English
https://www.adamgwilliam.dev/blog/last
https://getreuer.info/posts/keyboards/symbol-layer/index.html

https://www.reddit.com/r/ErgoMechKeyboards/comments/jghc1o/corne_layout_suggestions/
https://www.reddit.com/r/ErgoMechKeyboards/comments/192ts3d/corne_keyboard_mappings_for_programmers/
https://www.reddit.com/r/ErgoMechKeyboards/comments/1ip9sx4/suggestions_for_first_layout_on_my_first_corne/
https://www.teachmaths.org/20240715_34-key-layout-for-corne-keyboard/
