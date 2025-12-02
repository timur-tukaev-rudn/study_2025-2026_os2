---
## Front matter
title: "Отчёт по лабораторной работе №13"
subtitle: "Фильтр пакетов"
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

Получить навыки настройки пакетного фильтра в Linux.

# Выполнение

## Управление брандмауэром с помощью firewall-cmd

1. Получены права суперпользователя с помощью команды `su -`.  
   После ввода пароля выполнен переход в контекст root.

   ![Получение прав root](Screenshot_1.png){ #fig:firewall01 width=80% }

2. Определена текущая зона брандмауэра (`firewall-cmd --get-default-zone`).  
   Используется зона **public**.

3. Просмотрены все доступные зоны (`firewall-cmd --get-zones`).  
   Выведен список предустановленных зон.

4. Получен перечень всех поддерживаемых служб (`firewall-cmd --get-services`).  
   Выведен длинный список доступных сервисов.

5. Определены разрешённые в текущей зоне службы (`firewall-cmd --list-services`).  
   В зоне **public** активны: `cockpit`, `dhcpv6-client`, `ssh`.

6. Выполнено сравнение вывода команд `firewall-cmd --list-all` и `firewall-cmd --list-all --zone=public`.  
   Результаты совпадают, подтверждая, что активной зоной по умолчанию является **public**.

   ![Просмотр конфигурации зоны](Screenshot_2.png){ #fig:firewall02 width=80% }

7. Добавлена служба **vnc-server** во временную конфигурацию (`firewall-cmd --add-service=vnc-server`).  

8. Повторная проверка (`firewall-cmd --list-all`).  
   Служба **vnc-server** появилась в зоне.

9. Перезапущена служба firewalld (`systemctl restart firewalld`).  

10. После проверки (`firewall-cmd --list-all`) служба **vnc-server** исчезла.  
    Причина: добавление было сделано только во время выполнения (runtime), а не сохранено на диск.

    ![Добавление VNC](Screenshot_3.png){ #fig:firewall03 width=80% }

11. Служба VNC добавлена **постоянно** (`firewall-cmd --add-service=vnc-server --permanent`).  

12. Проверка (`firewall-cmd --list-all`) показала отсутствие изменений в runtime.  
    Постоянные настройки не применяются автоматически.

13. Загружена постоянная конфигурация и выполнена проверка (`firewall-cmd --reload`, затем `firewall-cmd --list-all`).  
    Служба **vnc-server** снова присутствует.

    ![Применение конфигурации](Screenshot_4.png){ #fig:firewall04 width=80% }

14. В постоянную конфигурацию добавлен порт **2022/tcp** (`firewall-cmd --add-port=2022/tcp --permanent`).  
    После загрузки конфигурации (`firewall-cmd --reload`) порт отображается в списке активных.

    ![Добавление порта 2022/tcp](Screenshot_5.png){ #fig:firewall05 width=80% }

## Управление брандмауэром с помощью firewall-config

1. Запущено приложение `firewall-config`.  
   При запуске потребовалось подтверждение прав администратора.

2. В интерфейсе выбрана конфигурация **Permanent**, чтобы изменения сохранялись на диске.

3. В зоне **public** активированы службы `http`, `https`, `ftp`.

   ![GUI firewall-config: включение служб](Screenshot_6.png){ #fig:firewall06 width=80% }

4. На вкладке *Ports* добавлен порт **2022** с протоколом UDP.

   ![Добавление порта UDP](Screenshot_7.png){ #fig:firewall07 width=60% }

5. Утилита закрыта, затем снова проверена текущая конфигурация (`firewall-cmd --list-all`).  
   Изменения ещё не отображаются (внесены как permanent).

6. Загружена конфигурация (`firewall-cmd --reload`).  
   После повторной проверки изменения применены: службы и порты отображаются в списке.

   ![Изменения вступили в силу](Screenshot_8.png){ #fig:firewall08 width=80% }

## Самостоятельная работа

Выполнена конфигурация доступа к службам:

- telnet — добавлена через командную строку (постоянно)
- imap, pop3, smtp — включены через `firewall-config`

После загрузки конфигурации все службы присутствуют в списке.

   ![Конечная конфигурация с telnet, imap, pop3, smtp](Screenshot_9.png){ #fig:firewall09 width=80% }

# Контрольные вопросы

1. Перед началом работы с менеджером конфигурации брандмауэра **firewall-config** должна быть запущена служба `firewalld`.

2. Добавление порта **2355/udp** в конфигурацию брандмауэра выполняется командой  
   `firewall-cmd --add-port=2355/udp`.

3. Для отображения полной конфигурации брандмауэра во всех зонах используется команда  
   `firewall-cmd --list-all-zones`.

4. Удаление службы **vnc-server** из текущей конфигурации выполняется командой  
   `firewall-cmd --remove-service=vnc-server`.

5. Активация новой конфигурации, сохранённой через опцию `--permanent`, происходит после выполнения команды  
   `firewall-cmd --reload`.

6. Проверить, что новая конфигурация применена и активна, можно командой  
   `firewall-cmd --list-all`.

7. Добавление интерфейса **eno1** в зону `public` выполняется командой  
   `firewall-cmd --zone=public --change-interface=eno1`.

8. Если зона не указана при добавлении нового интерфейса, он будет автоматически добавлен в **зону по умолчанию** (default zone).

# Заключение

В ходе лабораторной работы были изучены способы настройки межсетевого экрана с использованием инструментов `firewall-cmd` и `firewall-config`.  
Были рассмотрены различия между временной (runtime) и постоянной (permanent) конфигурациями, а также порядок применения изменений.  
На практике выполнены операции по добавлению и удалению служб и портов, назначению интерфейсов зонам и обновлению конфигурации брандмауэра.  
