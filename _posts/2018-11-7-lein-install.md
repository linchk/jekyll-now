---
layout: post
title: Настройка Leningen на Windows
---

На официальном сайте Leiningen.org описание установки под Windows содержит "вдохновляющую" фразу о том, что могут быть проблемы.
И их будет много! До тех пор пока вы не погрузитесь в понимание архитектуры и настроек данного проекта.
Надеюсь этот пост, поможет сэкономить время и упростить данный процесс.

# Настройка Leningen на Windows
**Прежде всего установите JAVA JDK https://www.oracle.com/technetwork/java/javase/downloads/index.html**

## Способы установки
Сам [Leiningen](Leiningen.org) можно установить несколькими способами:
- собрать из исходников, скачав их c Github *(не рекомендую для начинающих)* [technomancy/leiningen](https://github.com/technomancy/leiningen);
- скачать батник- инсталлятор с официального сайта и помахав бубном запустить его *(все бы хорошо, но не всегда он работает, иногда выдает ошибку и тогда придется устанавливать все вручную)*;
- скачать инсталлятор под windows  [djpowell.github.io/leiningen-win-installer](https://djpowell.github.io/leiningen-win-installer/) *(ссылки в официальных руководствах как правило ведут на 404 страницу, т.к. сайт переехал)*;
- установить вручную *(гарантированно будет работать!)*

### Собрать из исходников
Когда-нибудь я это сделаю, но пока Leiningen ругается на ошибку зависимостей и просит пересобрать пакет.

Если видите в ответ на попытку старта leiningen слово Bootstrap - значит это оно :)

```
Please run "lein bootstrap" in the leiningen-core/ directory
with a stable release of Leiningen. See CONTRIBUTING.md for details.
```

### Скачать батник с официального сайта
- Скачиваем [lein.bat](https://raw.githubusercontent.com/technomancy/leiningen/stable/bin/lein.bat) и кладём в папку на диске.
- Добавляем в переменную окружения $PATH путь к папке, где вы хотите установить leiningen.

***Указываем путь к папке а не к файлу,
  там же можно прописать путь к JDK и JDE***

  ![](/images/lein-setup/path.png)

- Запускаем консоль `ctrl+r cmd`
- И запустить инсталлятор `lein self-install`
- После загрузки проверяем версию `lein --version`
- И запускаем интерпретатор REPL `lein repl`

## Решаем проблемы
Обычно на Windows попытка запуска lein.bat self-install заканчивается следующим сообщение
`Downloading Leiningen now...`

``Исключение при вызове "DownloadFile" с "2" аргументами: "Запрос был прерван: Не удалось создать защищенный канал SSL/TLS."``


Причина в том, что Leiningen не может скачать нужные файлы из инета:
- на компьютере стоит wget
- powershell не поддерживает нужную версию протокола TLS (а он её не поддерживает! по умолчанию!!!)
- у вас соединения режет фаервол.

### Смена клиента для загрузки файлов

Можно попробовать починится командами, которые выдаёт leiningen в виде подсказок в консоли.

```
Try to use "curl" or "wget" to download Leiningen by setting up

the HTTP_CLIENT environment variable with one of the following

values:

  a) set HTTP_CLIENT=wget --no-check-certificate -O

  b) set HTTP_CLIENT=curl -f -L -k -o

NOTE: Make sure to *not* add double quotes when setting the value of HTTP_CLIENT
```

- Установите переменные окружения как указано в подсказке

```
C:\Users\Sergey>set HTTP_CLIENT=wget --no-check-certificate -O

C:\Users\Sergey>set HTTP_CLIENT=curl -f -L -k -o
```
- И запустите инсталлятор

```
C:\Users\Sergey>lein self-install
```

Процесс загрузки пошёл? - поздравляю!

### Изменение версии протокола TLS в powershell
Если нет, то можно переназначить протокол powershell
Путь к profile (Windows 10):

```
C:\Users\%USERNAME%\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1
```

Добавьте туда следующую строку:

```
[Net.ServicePointManager]::SecurityProtocol = [Net.ServicePointManager]::SecurityProtocol -bor [Net.SecurityProtocolType]::Tls12
```

### Ставим Leiningen вручную (самый простой способ!)

- Качаем [standalone JAR](https://github.com/technomancy/leiningen/releases)

***На момент статьи был leiningen-2.8.1-standalone.zip***

- Закидываем его в папку установки
- Переименовываем в JAR=>  leiningen-2.8.1-standalone.jar  
- Прописываем пути к lein в JDK

- Прописываем его в переменной окружения

![](/images/lein-setup/path.png)

```
HOME = %HOMEDRIVE%%HOMEPATH%
LEIN_JAR = %HOME%.lein\self-installs\leiningen-2.8.1-standalone.jar
```
(если вы ставили lein по умолчанию в домашнюю директорию пользователя в папку .lein)

```
LEINJAVA_CMD =C:\Program Files\Java\jdk-11\bin\java.exe
```
(путь к установленному JDK)

![](/images/lein-setup/lein-standalone.png)

Вроде бы ничего не забыл и все должно работать.
