---
## Front matter
lang: ru-RU
title: Лабораторная работа №9
subtitle: Планировщики событий
author:
  - Тукаев Тимур
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 9 октября 2025

## i18n babel
babel-lang: russian
babel-otherlangs: english

## Formatting pdf
toc: false
slide_level: 2
aspectratio: 169
section-titles: true
theme: metropolis
header-includes:
 - \metroset{progressbar=frametitle,sectionpage=progressbar,numbering=fraction}
---

# Цель работы

## Основная цель

Получение навыков работы с планировщиками событий **cron** и **at**.  
Изучение механизмов создания, редактирования и проверки запланированных задач в Linux.

# Планирование задач с помощью cron

## Изучение конфигурации /etc/crontab

![Просмотр содержимого /etc/crontab](Screenshot_1.png){ #fig:001 width=70% }

Файл содержит переменные окружения и пример синтаксиса cron-записи.

## Добавление нового задания

![Редактирование crontab для root](Screenshot_2.png){ #fig:002 width=70% }

Добавлена запись:
`*/1 * * * * logger This message is written from root cron`  
— выполнение каждую минуту.

## Проверка выполнения задания

![Проверка cron-заданий и системного журнала](Screenshot_3.png){ #fig:003 width=70% }

Сообщения фиксируются в системном журнале `/var/log/messages`.

## Изменение расписания

![Изменение расписания crontab](Screenshot_4.png){ #fig:004 width=70% }

Запись изменена на:  
`0 */1 * * 1-5` — выполнение каждый час по будням.

## Создание сценария eachhour

![Создание сценария eachhour](Screenshot_5.png){ #fig:005 width=70% }

Сценарий записывает сообщение в лог с текущей датой.  
Файл сделан исполняемым и добавлен в `/etc/cron.hourly`.

## Создание задания в /etc/cron.d

![Создание задания eachhour в /etc/cron.d](Screenshot_6.png){ #fig:006 width=70% }

Запись:  
`11 * * * * root logger This message is written from /etc/cron.d`  
— выполнение каждый час на 11-й минуте.

# Планирование заданий с помощью at

## Проверка службы atd

![Проверка статуса службы atd](Screenshot_7.png){ #fig:007 width=70% }

Задание отображается в списке `atq`.  
# Итоги работы

## Вывод

В ходе лабораторной работы:  
- изучены механизмы планирования задач в Linux;  
- освоена работа с планировщиками **cron** и **at**;  
- рассмотрены методы периодического и одноразового запуска команд;  
- отработаны приёмы контроля выполнения заданий через системные журналы.
