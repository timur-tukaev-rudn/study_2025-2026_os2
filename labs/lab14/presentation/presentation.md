---
## Front matter
lang: ru-RU
title: "Лабораторная работа №14"
subtitle: "Партиции, файловые системы, монтирование"
author:
  - Тукаев Тимур
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 14 ноября 2025

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

Получить навыки создания разделов, файловых систем и монтирования в Linux.

# Ход выполнения работы

## Просмотр дисков

![Просмотр дисков](Screenshot_1.png){ width=70% }

Определены доступные устройства `/dev/sdb` и `/dev/sdc`.

## Работа с fdisk: создание MBR-разделов

![Справка fdisk](Screenshot_2.png){ width=70% }

Использована утилита `fdisk` для разметки диска `/dev/sdb`.

## Создание основного раздела

![Создание основного раздела](Screenshot_3.png){ width=70% }

Создан основной раздел объёмом 300 MiB.

## Проверка таблицы разделов

![Проверка разделов](Screenshot_4.png){ width=70% }

Сравнение вывода `fdisk -l` и `/proc/partitions`.

## Создание расширенного и логического разделов

![Создание расширенного и логического разделов](Screenshot_5.png){ width=70% }

Созданы extended и logical-разделы.

## Проверка логических разделов

![Проверка логических разделов](Screenshot_6.png){ width=70% }

Разделы `/dev/sdb4` и `/dev/sdb5` отображаются корректно.

## Создание раздела подкачки

![Создание swap-раздела](Screenshot_7.png){ width=70% }

Создан логический раздел swap `/dev/sdb6`.

## Активация swap

![Активация swap](Screenshot_8.png){ width=70% }

Выполнены `mkswap` и `swapon`.

## Работа с GPT: разметка диска

![Просмотр GPT](Screenshot_9.png){ width=70% }

Использована утилита `gdisk` для создания GPT-раздела.

## Создание GPT-раздела

![Создание GPT-раздела](Screenshot_10.png){ width=70% }

Создан раздел размером 300 MiB.

## Проверка GPT

![Проверка GPT-разметки](Screenshot_11.png){ width=70% }

Раздел `/dev/sdc1` распознан системой.

## Форматирование XFS и EXT4

![Создание XFS и EXT4](Screenshot_12.png){ width=70% }

На `/dev/sdb1` создана XFS, на `/dev/sdb5` — EXT4.

## Ручное монтирование

Раздел `/dev/sdb5` смонтирован в `/mnt/tmp` и затем отмонтирован.

## UUID и настройка fstab

![UUID устройств](Screenshot_13.png){ width=70% }

Получены UUID и добавлена запись в `/etc/fstab`.

## Проверка fstab

![Редактирование fstab](Screenshot_14.png){ width=70% }

Автоматическое монтирование подтверждено.

## Проверка монтирования

![Проверка монтирования](Screenshot_15.png){ width=70% }

Раздел автоматически смонтирован в `/mnt/data`.

# Самостоятельная работа

## Создание GPT-партиций

![Создание GPT-разделов](Screenshot_16.png){ width=70% }

Созданы два раздела по 100 MiB.

## Форматирование ext4 и swap

![Форматирование ext4 и swap](Screenshot_17.png){ width=70% }

Раздел `/dev/sdc2` — EXT4, `/dev/sdc3` — swap.

## Настройка fstab

![Редактирование /etc/fstab](Screenshot_18.png){ width=70% }

Добавлены записи для автоматического монтирования.

## Проверка после перезагрузки

![Проверка разделов](Screenshot_19.png){ width=70% }

EXT4-раздел смонтирован, swap активен.

# Итоги работы

## Вывод

Были созданы MBR и GPT разделы, выполнено форматирование, настройка swap, ручное и автоматическое монтирование. Закреплены навыки работы с блочными устройствами и таблицами разделов в Linux.

