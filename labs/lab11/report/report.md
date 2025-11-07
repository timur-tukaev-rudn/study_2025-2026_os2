---
## Front matter
title: "Отчёт по лабораторной работе №11"
subtitle: "Управление загрузкой системы"
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

Получить навыки работы с загрузчиком системы GRUB2.

# Выполнение

## Модификация параметров GRUB2

1. Получены права администратора с помощью команды `su`.  
   После ввода пароля выполнен переход в контекст суперпользователя.  

2. В текстовом редакторе `mcedit` открыт файл конфигурации `/etc/default/grub`.  
   В параметре `GRUB_TIMEOUT` установлено значение **10** секунд.  
   Изменения сохранены, файл закрыт.  

   ![Редактирование параметров GRUB в файле /etc/default/grub](Screenshot_1.png){ #fig:001 width=80% }

3. Конфигурация загрузчика обновлена с помощью команды `grub2-mkconfig > /boot/grub2/grub.cfg`.  
   После выполнения команды сформирован новый файл конфигурации и добавлена запись для UEFI.  

   ![Обновление конфигурации GRUB2](Screenshot_2.png){ #fig:002 width=80% }

4. После перезагрузки системы при старте появилось меню **GRUB** с выбором версий ядра.  

   ![Меню загрузчика GRUB после перезагрузки](Screenshot_3.png){ #fig:003 width=80% }

## Устранение неполадок загрузки

5. Для загрузки в режим восстановления (**rescue.target**) выбрана текущая версия ядра и нажата клавиша **e**.  
   В строке, начинающейся с `linux`, добавлен параметр `systemd.unit=rescue.target`,  
   после чего загрузка продолжена с помощью **Ctrl+X**.  

   ![Добавление параметра rescue.target в конфигурации загрузки](Screenshot_4.png){ #fig:004 width=80% }

6. После успешной загрузки в режиме восстановления просмотрен список активных модулей с помощью `systemctl list-units`.  
   Отображается базовый набор системных служб.  

7. Просмотрены переменные среды с помощью команды `systemctl show-environment`.  

   ![Просмотр переменных среды](Screenshot_5.png){ #fig:005 width=80% }

8. После перезагрузки система вновь запущена с редактированием записи GRUB,  
   где в конце строки ядра добавлен параметр `systemd.unit=emergency.target`.  
   Загрузка продолжена сочетанием клавиш **Ctrl+X**.  

   ![Загрузка системы в режиме emergency.target](Screenshot_6.png){ #fig:006 width=80% }

9. После входа в систему просмотрен список загруженных модулей с помощью `systemctl list-units`.  
   Количество активных модулей сведено к минимуму.  

   ![Минимальный набор загруженных модулей в emergency.target](Screenshot_7.png){ #fig:007 width=80% }

## Сброс пароля root

10. При загрузке в режиме редактирования GRUB добавлен параметр `rd.break`  
    в конце строки ядра, после чего система остановилась на этапе `initramfs`.  

    ![Попытка выполнения chroot и passwd в initramfs](Screenshot_8.png){ #fig:008 width=80% }

11. Для монтирования корневой файловой системы с правом записи выполнена команда `mount -o remount,rw /sysroot`.  

12. Попытка перехода в корневую среду (`chroot /sysroot`) и изменения пароля (`passwd`)  
    завершилась ошибкой — команды отсутствовали в минимальной среде.  

    ![Попытка выполнения chroot и passwd в initramfs](Screenshot_9.png){ #fig:009 width=80% }

# Контрольные вопросы

1. Общие изменения для загрузчика GRUB2 вносятся в файл конфигурации `/etc/default/grub`.  

2. Основной конфигурационный файл, который используется системой при загрузке, — это `/boot/grub2/grub.cfg`.  

3. После внесения изменений в настройки GRUB2 необходимо выполнить команду `grub2-mkconfig > /boot/grub2/grub.cfg`, чтобы обновить конфигурацию загрузчика.  

# Заключение

В ходе лабораторной работы были изучены принципы настройки и управления загрузчиком GRUB2.  
Были рассмотрены способы изменения параметров загрузки, обновления конфигурации и устранения неполадок при запуске системы.  
Отработаны практические навыки загрузки системы в режимах восстановления и аварийного доступа, а также методы сброса пароля суперпользователя.  
Полученные результаты позволили закрепить знания о механизмах загрузки Linux и восстановлении доступа к системе.  
