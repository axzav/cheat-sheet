# ZMK

**ZMK main repo**
https://github.com/zmkfirmware/zmk.git

**Jorne config**
https://github.com/axzav/jorne-zmk-config

**ZMK Keymap Editor**
https://nickcoutsos.github.io/keymap-editor/

**Keymap Drawer**
https://github.com/caksoylar/keymap-drawer
https://keymap-drawer.streamlit.app/

**Build firmware locally:**
```shell
docker run -it --rm \
--security-opt label=disable \
--workdir /jorne-zmk-config \
-v ~/Home/Tools/jorne-zmk-config:/jorne-zmk-config \
-v ~/Home/Tools/tmp:/temp \
zmkfirmware/zmk-build-arm:3.5-branch /bin/bash

west init -l config && west updates
west zephyr-export


west build -d build/left -p always -s zmk/app -b nice_nano_v2 -- \
-DSHIELD=jorne_left \
-DZMK_CONFIG=/jorne-zmk-config/config

west build -d build/right -p always -s zmk/app -b nice_nano_v2 -- \
-DSHIELD=jorne_right \
-DZMK_CONFIG=/jorne-zmk-config/config

cp /jorne-zmk-config/build/left/zephyr/zmk.uf2 /temp/jorne_left.uf2
cp /jorne-zmk-config/build/right/zephyr/zmk.uf2 /temp/jorne_right.uf2
```
