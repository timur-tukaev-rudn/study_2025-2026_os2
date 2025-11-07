---
## Front matter
lang: ru-RU
title: Лабораторная работа №11
subtitle: Управление загрузкой системы (GRUB2)
author:
  - Тукаев Тимур
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 27 октября 2025

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

Получить навыки работы с загрузчиком системы GRUB2: изменение параметров загрузки, устранение неполадок и восстановление доступа.

# Ход выполнения работы

## Изменение параметров GRUB2

![Редактирование файла /etc/default/grub](Screenshot_1.png){ #fig:001 width=70% height=70% }

В файле `/etc/default/grub` установлен параметр `GRUB_TIMEOUT=10`.

## Обновление конфигурации GRUB2

![Обновление конфигурации загрузчика](Screenshot_2.png){ #fig:002 width=70% height=70% }

Команда `grub2-mkconfig > /boot/grub2/grub.cfg` пересоздала конфигурацию загрузчика.

## Проверка меню GRUB

![Меню загрузчика GRUB](Screenshot_3.png){ #fig:003 width=70% height=70% }

После перезагрузки появилось меню выбора ядра Linux.

## Режим восстановления (rescue.target)

![Добавление параметра rescue.target](Screenshot_4.png){ #fig:004 width=70% height=70% }

В режиме редактирования GRUB добавлен параметр `systemd.unit=rescue.target`.

## Работа в режиме восстановления

![Работа системы в режиме rescue.target](Screenshot_5.png){ #fig:005 width=70% height=70% }

Система загружена в базовую среду восстановления.

## Аварийный режим (emergency.target)

![Аварийный режим загрузки](Screenshot_6.png){ #fig:006 width=70% height=70% }

Добавлен параметр `systemd.unit=emergency.target` для минимальной загрузки системы.

## Минимальная система

![Минимальный набор модулей](Screenshot_7.png){ #fig:007 width=70% height=70% }

Система запущена с минимальным количеством модулей.

## Сброс пароля root

![Попытка выполнения chroot и passwd](Screenshot_9.png){ #fig:009 width=70% height=70% }

При загрузке с параметром `rd.break` выполнены попытки монтирования и сброса пароля суперпользователя.

# Итоги работы

## Вывод

Изучены принципы конфигурации и восстановления загрузчика GRUB2.  
Отработаны действия по изменению параметров, устранению ошибок загрузки и сбросу пароля root.  
Полученные навыки позволяют администратору эффективно управлять процессом загрузки Linux.
