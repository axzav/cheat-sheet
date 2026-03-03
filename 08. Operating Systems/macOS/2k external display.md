# 2k external display settings

В маке есть проблема с подключением внешних 2k мониторов. Из-за того, что эппл поддерживают только свои ретина дисплеи, шрифты в 1440p расплываются.
Исправить это невозможно, но можно поменять некоторые настройки, чтобы немного улучшить шрифты.

1. Установить **BetterDisplay**. Выставить разрешение 1152p в HiDPI mode. На таком разрешении получается лучший из доступных даунскейлов разрешения и картинка насколько четкая, насколько этого можно добиться изменением разрешения.

2. Поменять настройки шрифтов для **vscode/cursor**, предварительно установить в системе шрифт `JetBrains Mono`:

```json
    "editor.fontFamily": "'JetBrains Mono', monospace",
    "editor.fontSize": 14,
    "editor.lineHeight": 22,
    "editor.fontLigatures": true,
    "editor.fontWeight": "400",
    "editor.cursorSmoothCaretAnimation": "off",
    "editor.smoothScrolling": false
```

3. Поменять настройки шрифтов в **PHPStorm**:

Editor → Font:
* Font: JetBrains Mono
* Size: 14
* Line height: 1.15
