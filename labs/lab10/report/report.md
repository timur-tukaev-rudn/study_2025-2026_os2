---
## Front matter
title: "Отчёт по лабораторной работе №10"
subtitle: "Основы работы с модулями ядра операционной системы"
author: "Тукаев Тимур"

## Generic otions
lang: ru-RU
toc-title: "Содержание"

## Bibliography
bibliography: bib/cite.bib
csl: pandoc/csl/gost-r-7-0-5-2008-numeric.csl

## Pdf output format
toc: true
toc-depth: 2
lof: true
lot: true
fontsize: 12pt
linestretch: 1.5
papersize: a4
documentclass: scrreprt
## I18n polyglossia
polyglossia-lang:
  name: russian
  options:
    - spelling=modern
    - babelshorthands=true
polyglossia-otherlangs:
  name: english
## I18n babel
babel-lang: russian
babel-otherlangs: english
## Fonts
mainfont: IBM Plex Serif
romanfont: IBM Plex Serif
sansfont: IBM Plex Sans
monofont: IBM Plex Mono
mathfont: STIX Two Math
mainfontoptions: Ligatures=Common,Ligatures=TeX,Scale=0.94
romanfontoptions: Ligatures=Common,Ligatures=TeX,Scale=0.94
sansfontoptions: Ligatures=Common,Ligatures=TeX,Scale=MatchLowercase,Scale=0.94
monofontoptions: Scale=MatchLowercase,Scale=0.94,FakeStretch=0.9
mathfontoptions:
## Biblatex
biblatex: true
biblio-style: "gost-numeric"
biblatexoptions:
  - parentracker=true
  - backend=biber
  - hyperref=auto
  - language=auto
  - autolang=other*
  - citestyle=gost-numeric
## Pandoc-crossref LaTeX customization
figureTitle: "Рис."
tableTitle: "Таблица"
listingTitle: "Листинг"
lofTitle: "Список иллюстраций"
lotTitle: "Список таблиц"
lolTitle: "Листинги"
## Misc options
indent: true
header-includes:
  - \usepackage{indentfirst}
  - \usepackage{float}
  - \floatplacement{figure}{H}
---

# Цель работы

Получить навыки работы с утилитами управления модулями ядра операционной системы.

# Выполнение

## Управление модулями ядра из командной строки

1. Получены права администратора с помощью команды `su`.  
   После ввода пароля произведён переход в контекст суперпользователя.  

   ![Получение прав администратора и просмотр устройств](Screenshot_1.png){ #fig:001 width=80% }

2. Выполнена команда `lspci -k` для просмотра устройств системы и связанных с ними модулей ядра.  
   В результате выведен список контроллеров (VGA, Ethernet, Audio, USB и др.) с указанием используемых драйверов (`vmwgfx`, `e1000`, `snd_intel8x0`, `ahci`, `ata_piix` и т. д.).  

3. Проверен список загруженных модулей с помощью команды `lsmod | sort`.  
   Отображены все модули ядра и их зависимости.  

   ![Просмотр загруженных модулей](Screenshot_2.png){ #fig:002 width=80% }

4. Проверено наличие модуля `ext4` в системе командой `lsmod | grep ext4`.  
   На момент проверки модуль не был загружен.  

5. Загружен модуль ядра `ext4` при помощи `modprobe ext4`.  
   После этого повторная проверка `lsmod | grep ext4` показала, что модуль успешно загружен.  

   ![Загрузка и просмотр модуля ext4](Screenshot_3.png){ #fig:003 width=80% }

6. Информация о модуле `ext4` получена с помощью команды `modinfo ext4`.  
   Отображены сведения о лицензии (GPL), авторах, версиях, зависимостях (`jbd2`, `mbcache`), а также параметрах и пути к бинарному файлу `/lib/modules/.../ext4.ko.xz`.  

7. Попытка выгрузки модуля `ext4` (`modprobe -r ext4`) выполнена несколько раз.  
   Система не выдала ошибок, но при попытке выгрузить модуль `xfs` появилось сообщение:  
   **FATAL: Module xfs is in use**, что означает, что данный модуль используется файловой системой и не может быть выгружен.  

   ![Выгрузка модулей ext4 и xfs](Screenshot_4.png){ #fig:004 width=80% }

## Загрузка модулей ядра с параметрами

1. Проверено наличие модуля `bluetooth` командой `lsmod | grep bluetooth`.  
   До загрузки модуль отсутствовал в списке.  

2. Модуль `bluetooth` загружен с помощью `modprobe bluetooth`.  
   После этого команда `lsmod | grep bluetooth` подтвердила успешную загрузку модуля и зависимого компонента `rfkill`.  

3. Подробная информация о модуле `bluetooth` получена при помощи `modinfo bluetooth`.  
   В выводе указаны параметры:
   - `disable_esco` — отключает создание eSCO-соединений;  
   - `disable_ertm` — отключает режим Enhanced Retransmission;  
   - `enable_ecred` — включает режим Enhanced Credit Flow Control.  

   ![Информация о модуле bluetooth](Screenshot_5.png){ #fig:005 width=80% }

4. После проверки модуль был выгружен с помощью `modprobe -r bluetooth`.  
   Ошибок при выгрузке не возникло.  

   ![Выгрузка модуля bluetooth](Screenshot_6.png){ #fig:006 width=80% }

## Обновление ядра системы

1. Проверена текущая версия ядра командой `uname -r`.  
   Используется ядро версии `6.12.0-55.12.1.el10_0.x86_64`.  

2. Просмотрен список пакетов, относящихся к ядру, при помощи `dnf list kernel`.  
   В списке доступных пакетов присутствует новая версия `6.12.0-55.39.1.el10_0.x86_64`.  

   ![Просмотр пакетов ядра](Screenshot_7.png){ #fig:007 width=80% }

3. Выполнены обновления системы и ядра командами `dnf upgrade --refresh`, `dnf update kernel`, `dnf update`.  
   Все зависимости успешно разрешены, пакеты обновлены без ошибок.  

   ![Обновление ядра и системы](Screenshot_8.png){ #fig:008 width=80% }

4. После перезагрузки системы проверена версия установленного ядра командой `uname -r` и общая информация о системе через `hostnamectl`.  
   Подтверждено использование новой версии ядра `6.12.0-55.39.1.el10_0.x86_64`.  

   ![Проверка версии ядра и системы](Screenshot_9.png){ #fig:09 width=80% }


# Контрольные вопросы

1. Текущую версию ядра, используемую в системе, показывает команда `uname -r`.  

2. Более подробную информацию о текущей версии ядра можно получить с помощью команды `hostnamectl`.  

3. Список загруженных модулей ядра отображает команда `lsmod`.  

4. Параметры модуля ядра определяются командой `modinfo`.  

5. Для выгрузки модуля ядра используется команда `modprobe -r <имя_модуля>`.  

6. Если при попытке выгрузить модуль появляется сообщение об ошибке, это означает, что модуль используется. В таком случае необходимо остановить процессы, использующие данный модуль, либо выполнить выгрузку после перезагрузки системы.  

7. Поддерживаемые параметры модуля можно определить с помощью команды `modinfo <имя_модуля>`, где в выводе указаны строки `parm:`.  

8. Установить новую версию ядра можно с помощью команд `dnf update kernel` или `dnf upgrade --refresh`, после чего требуется перезагрузить систему.  

# Заключение

В ходе лабораторной работы были изучены основные принципы управления модулями ядра операционной системы Linux.  
Освоены команды для просмотра подключённых устройств и связанных с ними драйверов, загрузки и выгрузки модулей, а также получения подробной информации о них с помощью `modinfo`.  
Были исследованы зависимости и параметры модулей, определено их назначение и особенности работы.  
