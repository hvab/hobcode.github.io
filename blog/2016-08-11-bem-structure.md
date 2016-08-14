# Структура проекта по БЭМ для верстальщика

Хочу в этой теме обсудить структуру проекта по бэму для верстальщика. Верстальщиком называю человека, который превращает PSD в html/css и немного js. Фронтендеры пишут жирный JS и БЭМ :)

Начну из далека. У обычных верстальщиков структура примерно такая (взято из TARS):

```sh
static/                                     #  Folder for static-files. You can choose the name for that folder in tars-config.js
    └── fonts/                              # Fonts (can contain subdirectories)
    └── img/                                # Images. You can choose the name for that folder in tars-config.js
        └── content/                        # Images for content (can contain subdirectories)
        └── plugins/                        # Images for plugins (can contain subdirectories)
        └── general/                        # General images for project (can contain subdirectories)
        └── sprite/                         # Raster images, which is included in png-sprite.
            └── 96dpi/                      # Images for displays with dpi 96
            ...
            └── 384dpi/                     # Images for displays with dpi 384 (more info in images-processing)
        └── svg/                            # SVG-images
    └── js/                                 # js
        └── framework/                      # js-frameworks (backbone, for example)
        └── libraries/                      # js-libraries (jquery, for-example)
        └── plugins/                        # js-plugins
        └── separate-js/                    # js-files, which must not be included in ready bundle
    └── misc/                               # General files, which will be moved to root directory of ready project — favicons, robots.txt and so on  (can contain subdirectories)
    └── scss  
        ├── entry/                          # Styles for entry points for css in case of manual css-processing More info [here](css-manual-processing.md).                
        └── etc/                            # Styles, which will be included in the end of ready css-file (can contain subdirectories)
        └── libraries/                      # Styles for libraries (can contain subdirectories)
        └── plugins/                        # Styles for plugins (can contain subdirectories)
        └── sprite-generator-templates/     # Templates for sprite generating
        └── sprites-scss/                   # Mixins for sprites
        ├── separate-css/                   # Css-files, which must not be included in ready bundle
        ├── common.scss                     # General styles
        ├── fonts.scss                      # Styles for fonts
        ├── GUI.scss                        # Styles for GUI elements (inputs, buttons and so on)
        ├── mixins.scss                     # Project's mixins
        ├── normalize.scss                  # Styles reset
        ├── vars.scss                       # Variables
```

Всё разделено по реализациям и технологиям, и всё инклюдится :)

В бэме эту структуру перевернули с ног на голову и засунули всё в блок.

```sh
button
  button.css
  button.js
```

Допустим у нас одностраничный сайт, и все блоки лежат плоской структурой. Уровней переопределения нет.

```sh
project
  index.html
  static
    header
    footer
    button
      button.css
      button.js
```

Если у нас несколько страниц сайта, то появляется необходимость выделить блоки страниц на отдельный уровень переопределения. К тому же, в этом проекте воспользовались несколькими сторонними библиотеками блоков, которые тоже следует выделить на отдельный уровень переопределения. Уже на этом этапе встает вопрос о том, как удобней и универсальней это сделать.

Первый вариант я подсмотрел в исходниках сайта [bem.info](https://github.com/bem-site/bem.info/tree/master/blocks).

```sh
project
  about.html
  index.html
  static
    blocks
      about               # 3 level
        button
      common              # 2 level
        button
      index               # 3 level
        button
    libs                  # 1 level
      foo-components
        button
```

Для меня основное неудобство этого варианта в том, что общие блоки `common` визуально смешиваются и теряются в дереве каталогов страниц.

Второй вариант - это явно указать где что лежит.

```sh
project
  about.html
  index.html
  static
    common                # 2 level
      button
    libs                  # 1 level
      foo-components
        button
    pages                 # 3 level
      about
        button
      index
        button
```

Остановимся на втором варианте. Теперь нам нужно сделать мобильную версию сайта. У нас уровни переопределения будут разветвляться на платформы `desktop` и `mobile`. При этом блоки `common` из общих для всех страниц, превращаются в общие для платформ.

Вариант "в лоб".

```sh
project
  about.html
  index.html
  static
    common                # 2 level
      button
    libs                  # 1 level
      foo-components
        button
    desktop
      common              # 3a level
        button
      pages               # 4a level
        about
          button
    mobile
      common              # 3b level
        button
      pages               # 4b level
        about
          button
```

Я считаю, что такой вариант имеет место быть, если вы не боитесь большой вложенности. Даже можно выделить в плюсы тот факт, что для реализации платформы `mobile` достаточно всего лишь дублировать папку `desktop`, переименовать и начать переписывать блоки.

Можно эту структуру представить в плоском виде. При этом в названиях папок через точку определять ее "тип". В этом месте мы подбираемся к ["бэму головного мозга"](https://ru.bem.info/forum/-233/). Добавим к этому, что источников библиотек у нас стало несколько (допустим, они не пересекаются в плане используемых блоков).

```sh
project
  about.html
  index.html
  static
    bower.libs
      foo-components
        button
    common.blocks
      button
    desktop.blocks
      button
    desktop.pages
      about
        button
    mobile.blocks
      button
    mobile.pages
      about
        button
    npm.libs
      bar-components
        input
```

Схлопнутый вариант:

```sh
bower.libs
common.blocks
desktop.blocks
desktop.pages
mobile.blocks
mobile.pages
npm.libs
```
Если для генерации html использовать шаблонизаторы, то их можно хранить внутри блоков, либо как технология проекта `*.templates`.

Резюмируя, у меня получается такая структура проекта. Для собранной верстки с последующей отдачей бэкендерам, можно предусмотреть папки в технологии `*.dist`.

```sh
bower.libs
common.blocks
common.templates
desktop.blocks
desktop.dist
desktop.pages
desktop.templates
mobile.blocks
mobile.dist
mobile.pages
mobile.templates
npm.libs
```

Хочется определиться со структурой и придерживаться ее во всех проектах любой сложности. Надеюсь на вашу помощь в плане того, правильно ли я понял и применяю идею технологий реализации проекта как блока. Также очень интересно узнать как бы вы по бэму положили в проект шрифты, спрайты, переменные и миксины (SCSS, LESS).
