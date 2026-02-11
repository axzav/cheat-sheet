202012211407
Tags: #
____________________________________________________
# PHP Standard Recommendation
https://www.php-fig.org/psr/
https://github.com/php-fig
https://elisdn.ru/blog/134/four-pillars-of-psrs - 4 столпа PSR

Psr расшифровывается как PHP Standards recommendations. Эти рекомендации разрабатывает организация PHP-FIG (The php framework interop group), которая была основана в 2009 году. В организации состоят разработчики некоторых фреймворков и библиотек (composer, yii, zend, etc). Целью этих рекомендаций является унификация стиля кодирования и установление соглашений, который позволяют включать в свой проект, переиспользовать и переносить модули и классы, созданные другими разработчиками (в случае, если они придерживаются этих рекомендаций).
На данный момент (весна 2019) существует 13 утвержденных документов:

PSR-1
Basic Coding Standard
Основные стандарты оформления кода. Основные из них:
Файлы должны использовать только `<?php` и `<?=` теги
Пространство имен должно следовать PSR-4
Имена классов в StudlyCaps
Имена методов в camelCase
Константы классов в верхнем регистре, слова разделены _

PSR-2
Coding Style Guide
Расширенные стандарты оформления кода. Основные:
4 пробела вместо табов
Открывающие/закрывающие скобки класса/метода должны располагаться на следующей строке
Один пробел после объявления пространства имен, один пробел после блока импорта пространства имен

PSR-3
Logger Interface
Этот интерфейс позволяет использовать универсальный путь для логгирования в приложении и подключаемых модулях. Т.е. если в приложении используется объект логгера, реализующего интерфейс Psr\Log\LoggerInterface, то этот объект может быть передан в подключаемый модуль, который пишет логи с использованием того же интерфейса Psr\Log\LoggerInterface

PSR-4
Autoloading Standard
Этот стандарт устанавливает правила именования пространства имен и расположения файла относительно пространства имен. Этот стандарт позволяет использовать единую логику автозагрузчика для классов приложения и подключаемых модулей. Основные положения:

\<NamespaceName>(\<SubNamespaceNames>)*\<ClassName>
ClassName - класс, интерфейс или трейт
NamespaceName - vendor name
SubNamespaceNames - эта часть пространства имен должна отражать путь до класса в файловой системе

PSR-6
Caching Interface


PSR-7
HTTP Message Interface


PSR-11
Container Interface


PSR-13
Hypermedia Links


PSR-14
Event Dispatcher


PSR-15
HTTP Handlers


PSR-16
Simple Cache


PSR-17
HTTP Factories


PSR-18
HTTP Client



Все PSR интерфейсы можно найти в репозитории https://github.com/php-fig и включить в свой проект.

# PSR & PHP-FIG
https://art-lemon.com/chto-takoe-php-fig
https://www.php-fig.org/

## Codestyle standards

https://www.php-fig.org/psr/psr-1/ PSR-1
https://www.php-fig.org/psr/psr-12/ PSR-12
Symfony - https://symfony.com/doc/current/contributing/code/standards.html






____________________________________________________
### Base category
- [[00 Job interview]]
____________________________________________________
### Links
- [[PHP]]