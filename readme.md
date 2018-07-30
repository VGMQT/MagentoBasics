##### 1) Структура темы. Как сделать extend стилей? Где меняются общие стили сайта, меню, линки, breadcrumbs и т.д.?
```
app/design/frontend/Vendor/Custom_Theme/
├── etc/
│ ├── view.xml
├── i18n/
├── media/
├── web/
│ ├── css/
│ │ ├── source/ 
│ ├── fonts/
│ ├── images/
│ ├── js/
├── composer.json
├── registration.php
├── requirejs-config.js
├── theme.xml
```
Файл ```app/web/css/source/_extend.less``` используется для добавления стилей родительской теме, однако, не их замены.
В то время как файл ```app/web/css/source/_theme.less``` используется для перезаписи уже существующих стилей. Именно
в этом файле меняются стили всех внутренних элементов, уже прописанных в родительской теме.
Общие стили сайта меняются в админке Magento 2 ```Content -> Design -> Configuration```

##### 2) Создавали ли тему с нуля?
Да, несколько раз.

##### 3) Рассказать про наследование тем.
Родительские темы прописываются в файле ```theme.xml```. Уровней наследования может быть сколько угодно.
```
<theme xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:Config/etc/theme.xsd">
   <title>Custom Theme</title>
   <parent>Magento/blank</parent>
</theme>
```

##### 4) Работали ли с мультисайтами?
Нет, но общее понятие имею.

##### 5) Писали ли инсталл-скрипты?
Нет, есть небольшие затруднения в понимании, что это такое.

##### <a name="6">6) Как заэкстендить виджет?</a>
Использовать RequireJS.

```requirejs-config.js example```
```
const config = {
    "map": {
        "*": {
            "menu": "js/menu-custom"
        }

    }
};
```

```extend module placement```
```
├── web/
│ ├── js/
│ │ ├── menu-custom.js
```

```extend module example```
```
define([
        'jquery',
        'jquery/ui',
        'mage/menu' ],
    function($){
        $.widget('custom.menu', $.mage.menu, {
            _init: function () {
                console.log("Custom menu loaded");
            },
            toggle: function () {
                console.log("Custom menu loaded");
            }
        });
        return $.custom.menu;
    });
```

##### 7) Как заэкстендить UI-компонент?
Созданием такого же файла UI-элемента в подкаталоге нашей собственной темы. Также следует создать для него отдельную папку,
название которой наследует оригинальный модуль. Например:
```
module-catalog -> Module_Catalog
module-checkout -> Module_Checkout
```
Мы можем либо заэкстендить компонент, дописав/удалив определённые параметры, либо полностью его заменить нашей
собственной копией.

##### 8) Как перезаписывать (override) JavaScript?
Здесь всё также, как и в пункте [6)](#6). Однако, в новом файле можно как добавлять функционал, так и менять
его поведение целиком, перезаписывая функции.

##### 9) Переводы.
Перевод темы осуществляется с помощью словарей перевода. Словари с переводами находятся в директории темы в папке
```/i18n/```. Папка ```/i18n/``` может находиться в каждом модуле или глобально в папке ```app/```.

Генерация файла с переводами:
```
php bin/magento i18n:collect-phrases --output="app/design/frontend/Vendor/Custom_Theme/i18n/en_US.csv" app/design/frontend/Vendor/Custom_Theme
```

##### 10) Наследование модулей (вендорных и экстеншенов).
Не совсем понятен смысл вопроса. Однако, о замене и экстенде модулей через папку ```/templates/``` в курсе.

##### 11) Как дела вообще? Любишь мадженту? А за что ты ее так любишь?
Дела отлично. По-тихоньку работаю над мелкими задачами на drupal-проектах. С Magento осваиваться без реальных проектов,
задач и фидбэка сложновато. Хоть основы и, примерно, понятны, но насчёт того, как себя вести в реальном проекте, пока
нет идей. Пока отношение нейтральное, так как видна куча замудрённостей, которые на данный момент логически не объяснены.
Думаю, будет гораздо лучше видно что к чему именно при работе с ней.

##### 12) Есть сайт с кастомной темой. Клиент захотел на время рождественских праздников поменять фон на всём сайте. Как?
Смотря что используется в качестве фона. С тайминговыми сменами стилей пока не знаком, однако, поменять цвет фона или
бэкграунд-изображение не составит труда, так как это всё можно сделать в одном файле кастомной темы:
```/web/css/source/_theme.less```

##### 13) Принцип работы Require JS?
Один JS-файл на одну страницу сайта, в котором определяются необходимые для работы конкретно этой страницы JavaScript-модули.
Сами модули с помощью функций ```define(); require()``` определяют необходимые зависимости и реализуют нужный код.

##### 14) Добавление стилей на сайт.
Magento имеет свою библиотеку UI-элементов, изменения в которой можно делать, меняя значения переменных на свои
собственные, клонируя ```.less``` файлы в директорию своей темы. Также можно добавлять свои собственные файлы стилей,
указывая их в ```default_head_blocks.xml``` (копии, которая также будет находиться в директории нашей темы).

##### 15) Media queries.
Не самый понятный аспект без очевидных примеров.

Из того, что я понял, медиа-запросы завязаны на использовании определённых миксинов непосредственно в файлах стиля,
где они нужны (либо выносом в отдельные). По итогу они всё равно будут компилироваться в один файл ```style-m.less``` и
```style-l.less```, для мобильных и десктопных версий относительно. Используемый метод — Mobile First.

```
.media-width(@extremum, @break) when (@extremum = 'max') and (@break = @screen__s) {
    your styles
}
```

```
// For targetting device for styles output
@media-target: all|desktop|mobile
 
// In practice
& when (@media-target = 'mobile'), (@media-target = 'all') {
    @media only screen and (max-width: (@screen__xs - 1)) {
        .media-width('max', @screen__xs);
    }
}
 
// For deciding whether to output common styles.
@media-common: true|false
 
// Adding common styles
& when (@media-common = true) {
    your styles
}
```
##### 16) Добавление JS.
Есть несколько способов:
1. Вызов скрипта в ```.phtml``` template
    ```
    <script type="text/x-magento-init">
    {
        "*": {
            "Namespace_Module/js/jsfilename": {}
        }
    }
    </script>
    ```
    jsfilename.js
    ```
    require([],
    function(){
        "use strict";
        //your js goes here
    });
    ```
2. Вызов скрипта в ```.phtml``` template c clear target element, но без параметров

    Option 1
    ```
    <script type="text/x-magento-init">
    {
        "#some-element-id": { 
            "Namespace_Module/js/jsfilename": {} 
        } 
    } 
    </script>
    ```
    Option 2
    ```
    <div id="some-element-id" 
             data-mage-init='{"Namespace_Module/js/jsfilename":{}}'>
        <!-- some elements and content -->
    </div>
    ```
    jsfilename.js
    ```
    define(
    [
        'uiComponent'
    ],
    function (Component) {
        'use strict';
    
        return Component.extend({
    
            initialize: function (config, node) {
                node; //#some-element-id
            }
        });
    });
    ```
3. Вызов скрипта в ```.phtml``` template с передачей параметров

    Option 1
    ```
    <script type="text/x-magento-init">
    {
        "#some-element-id": {
            "Namespace_Module/js/jsfilename": {"parameter":"value"} 
        } 
    } 
    </script>
    ```
    Option 2
    ```
    <div id="some-element-id" 
             data-mage-init='{"Namespace_Module/js/jsfilename":{"parameter":"value"}}'>
        <!-- some elements and content -->
    </div>
    ```
    jsfilename.js
    ```
    define(
    [
        'uiComponent'
    ],
    function (Component) {
        'use strict';
    
        return Component.extend({
    
            initialize: function (config, node) {
                var value = config.parameter; 
                node; //#some-element-id
            }
        });
    });
    ```
4. Перезапись JS-файла

    requirejs-config.js
    ```
    var config = {
        map: {
            '*': {
                'Magento_ModuleName/js/original-file-name':
                'Namespace_Module/js/original-file-name'
            }
        }
    };
    ```
    
    Копия уже существующего JS-файла со всем оригинальным контентом
    ```Namespace/Module/view/frontend/web/js/original-file-name.js```
5. Перезапись/Экстенд виджета

    Оригинальный файл
    ```module-checkout/view/frontend/web/js/sidebar.js```
    
    Наш файл
    ```app/code/Namespace/Checkout/view/frontend/web/js/sidebar-mixin.js```
    
    requirejs-config.js
    ```
    var config = {
           config: {
               mixins: {
                   'Magento_Checkout/js/sidebar': {
                       'Namespace_Module/js/sidebar-mixin': true
                   }
               }
           }
       };
    ```
    
    sidebar-mixin.js
    ```
    define([
        'jquery'
    ],
    function($){
        'use strict';
        return function (widget) {
            $.widget('mage.sidebar', widget, {
                _initContent: function () {
                    //add new functionality or extend using this._super()
                }
            });
            return $.mage.sidebar;
        }
    });
    ```
6. Перезапись/Экстенд методов компонента

    requirejs-config.js
    ```
    var config = {
        config: {
            mixins: {
                'Magento_Ui/js/form/form': { 
                    'Namespace_Module/js/form-mixin': true
                }
            }
        }
    };
    ```
    
    В form-mixin.js мы можем перезаписать любой метод компонента ```form```
    ```
    define([],
       function(){
           'use strict';
           return function (Component) {
               return Component.extend({
                       initialize: function () {
                       //add new functionality or extend using this._super()
                   }
               });
           }
       });
    ```
##### 17) Работали ли с Knockout? Что именно делали?
Не работали, я только ознакомился. Сделал первый туториал.

