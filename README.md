Набор скриптов для автоматической генерации xPDO моделей и сборки компонента MODx.

Работает только с mySQL, так как используется информация из базы данных information_schema.

## Установка

По-умолчанию папка modxbuilder должна располагаться в каталоге на уровень выше вашей корневой папки сайта (www).

```
.
|-modxbuilder
| |-mycomponent
| | |-_build
| | |-_core
| | |-tools
|
|-www
  |-assets
  |-connectors
  |-core
  |-manager
  |-config.core.php
  |-index.php
```

После того, как вы склонировали репозиторий в папку modxbuilder, можно приступать к работе

## Определяем имя компонента, префикс классов и префикс таблиц

Перед тем как создавать свои таблицы в БД, необходимо определиться с названием вашего расширения, префиксом таблиц и префиксом генерируемых классов

Например, вы хотите назвать ваш компонент `yourcomponent`, префикс классов `your`, префикс таблиц `your_`:

1. Переименуйте папку `modxbuilder/mycomponent` в `modxbuilder/yourcomponent`
2. Переименуйте папку `modxbuilder/yourcomponent/_core/components/mycomponent` в `modxbuilder/yourcomponent/_core/components/yourcomponent`
3. Откройте файл `modxbuilder/yourcomponent/_build/build.config.php` и укажите соответствующие значения в переменных `$package_name` `$package_table_prefix` `$package_class_prefix`

## Создание пользовательских таблиц

Создайте в вашей базе данных таблицу(-ы) с именем в формате:

```
[глобальный_префикс][префикс_ваших_таблиц][имя_таблицы]
```

Например:

```
modx_your_books
```

При создании таблицы вы можете явно указать класс будущей модели через поле комментария к таблице. Если его не указать, то имя класса будет сформировано
автоматически из названия таблицы. В нашем случае имя класса будет yourBooks.

Поскольку books - это множественное число, то вы, вероятно, хотели бы, чтобы ваш класс назывался yourBook. Выхода из этой ситуации два:

1. Изменить название таблицы на modx_your_book
2. Указать в комментариях к таблице modx_your_book значение yourBook. В этом случае, независимо от названия таблицы (после modx_your_) имя вашей модели
будет четко указано - yourBook

## Генерация первичной xml-схемы

После того, как вы создали все необходимые таблицы с префиксом modx_your_ (или другим, выбранным вами), запускаем скрипт `modxbuilder/[имя_компонента]/_build/build.php`

При успешном выполнении скрипта, вы получите файл `modxbuilder/[имя_компонента]/_core/components/[имя_компонента]/model/schema/[имя_компонента].mysql.schema.new.xml`

## Создание основной xml-схемы

После того, как вы получили файл первичной схемы, вам необходимо скопировать содержимое файла `modxbuilder/[имя_компонента]/_core/components/[имя_компонента]/model/schema/[имя_компонента].mysql.schema.new.xml`
в файл `modxbuilder/[имя_компонента]/_core/components/[имя_компонента]/model/schema/[имя_компонента].mysql.schema.xml`.

При необходимости, дополните содержимое файла `modxbuilder/[имя_компонента]/_core/components/[имя_компонента]/model/schema/[имя_компонента].mysql.schema.xml`
дополнительной информацией о связях между таблицами и полями валидации.

[Подробнее об описании связей между моделями](https://docs.modx.com/xpdo/2.x/getting-started/creating-a-model-with-xpdo/defining-a-schema/defining-relationships)

[Подробнее о правилах валидации моделей](https://docs.modx.com/xpdo/2.x/getting-started/creating-a-model-with-xpdo/defining-a-schema/validation-rules-in-your-schema)

## Генерация моделей

После того, как вы добавили содержимое в файл `modxbuilder/[имя_компонента]/_core/components/[имя_компонента]/model/schema/[имя_компонента].mysql.schema.xml`
снова запустите скрипт `modxbuilder/[имя_компонента]/_build/build.php`.

После успешного запуска вашего скрипта в папке `modxbuilder/[имя_компонента]/_core/components/[имя_компонента]/model/[имя_компонента]/` появятся все
сгенерированные файлы моделей и маппинги mysql.

## Использование моделей в проекте

Как вы заметили, вся описанная выше работа изолирована в каталоге `modxbuilder/[имя_компонента]`. Для дальнейшего использования
ваших моделей в проекте, вам необходимо:

1. Если сборка производится впервые и в папке www/core/components еще нет папки с вашим компонентом, то просто скопируйте содержимое каталога
`modxbuilder/[имя_компонента]/_core/components/` в каталог `www/core/components/`
2. Если сборка производится не впервые и в процессе разработки файлы в каталоге `www/core/components/[имя_компонента]/` претерпели ряд изменений, таких, например, как
описание дополнительных методов внутри моделей или создание дополнительных классов, не связанных с базой данных, то вам необходимо только лишь переносить изменения из
каталога `modxbuilder/[имя_компонента]/_core/components/[имя_компонента]/` в каталог `www/core/components/[имя_компонента]`. Удобнее всего это делать при помощи функции сравнения каталогов в phpStorm.