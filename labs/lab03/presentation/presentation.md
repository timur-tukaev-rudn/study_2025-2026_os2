---
## Front matter
lang: ru-RU
title: Лабораторная работа №3
subtitle: Настройка прав доступа в Linux
author:
  - Тукаев Тимур
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 9 декабря 2025

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

Получить практические навыки настройки базовых, специальных и расширенных прав доступа  
в операционной системе Linux для пользователей и групп.

# Выполнение работы

## Создание структуры каталогов

![Создание каталогов и смена владельцев](Screenshot_1.png){ #fig:001 width=70% }

Созданы каталоги `/data/main` и `/data/third`, изменены группы и назначены права `770`.

## Работа пользователя bob

![Работа bob в каталоге main](Screenshot_2.png){ #fig:002 width=70% }

Пользователь bob может создавать файлы в `/data/main`,  
но не имеет доступа к `/data/third`.

## Создание файлов в общем каталоге

![Удаление файлов alice до назначения sticky-bit](Screenshot_2.png){ #fig:003 width=70% }

До включения sticky-bit пользователь bob может удалять файлы alice.

## SGID и sticky-bit

![SGID и sticky-bit в /data/main](Screenshot_3.png){ #fig:004 width=70% }

Группа `third` получила доступ `rx` к `/data/main`,  
а группа `main` — права `rx` к `/data/third`.

## Создание файлов без ACL по умолчанию

![ACL newfile1](Screenshot_4.png){ #fig:006 width=70% }

Первые файлы наследуют обычные POSIX-права,  
так как ACL по умолчанию ещё не настроены.

## ACL по умолчанию

![Наследование ACL для newfile2](Screenshot_5.png){ #fig:007 width=70% }

После настройки ACL по умолчанию новые файлы наследуют групповые права.

## Попытки удаления и записи

![Права пользователя carol](Screenshot_6.png){ #fig:008 width=70% }

Пользователь carol не может удалять или изменять файлы —  
это блокируется sticky-bit и отсутствием прав `w`.

# Итоги работы

## Вывод

В ходе лабораторной работы изучены механизмы управления доступом в Linux:  
базовые права, SGID, sticky-bit, а также ACL и их наследование.  
Полученные навыки позволяют эффективно организовывать безопасный  
многопользовательский доступ к файловой системе.
