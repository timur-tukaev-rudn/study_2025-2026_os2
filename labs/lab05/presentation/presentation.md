---
## Front matter
lang: ru-RU
title: Лабораторная работа №5
subtitle: Управление системными службами systemd
author:
  - Тукаев Тимур
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 10 декабря 2025

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

Получить навыки управления системными службами и целями  
в операционной системе Linux с использованием systemd.

# Управление сервисами

## Проверка статуса и установка vsftpd

![Статус и установка vsftpd](Screenshot_1.png){ width=70% }

Служба отсутствовала в системе — затем была установлена через DNF.

## Запуск и проверка работы службы

![Статус после запуска](Screenshot_2.png){ width=70% }

После запуска `vsftpd` стала активной, но не включённой в автозагрузку.

## Управление автозапуском vsftpd

![Enable/Disable vsftpd](Screenshot_3.png){ width=70% }

Выполнены операции `enable` и `disable`, проверено изменение состояния юнита.

## Создание символических ссылок для автозагрузки

![Создание ссылки vsftpd](Screenshot_4.png){ width=70% }

После `systemctl enable vsftpd` появилась ссылка в  
`multi-user.target.wants`.

## Зависимости юнита vsftpd

![Зависимости vsftpd](Screenshot_5.png){ width=70% }

Изучены зависимости юнита и обратные зависимости.

# Конфликты юнитов

## Статусы firewalld и iptables

![Статусы служб](Screenshot_6.png){ width=70% }

Сервисы конфликтуют — одновременный запуск невозможен.

## Конфликты в файле firewalld.service

![Юнит firewalld](Screenshot_7.png){ width=70% }

Наличие `Conflicts=iptables.service`.

## Конфигурация iptables.service

![Юнит iptables](Screenshot_8.png){ width=70% }

Структура юнита и условия его загрузки.

## Маскирование iptables

![Маскирование iptables](Screenshot_9.png){ width=70% }

Сервис замаскирован — запуск невозможен.

# Изолируемые цели

## Поиск целей с AllowIsolate=yes

![Поиск изолируемых целей](Screenshot_10.png){ width=70% }

Определены цели, допускающие изоляцию.

## Переход в rescue.target

![Переход в rescue.target](Screenshot_11.png){ width=70% }

Система переведена в режим восстановления.

## Переключение в reboot.target

Система перезагружена через изоляцию цели reboot.

# Цель по умолчанию

## Проверка текущего режима загрузки

![Проверка default target](Screenshot_12.png){ width=70% }

Определён текущий default target.

## Установка текстового режима загрузки

Система переведена на `multi-user.target`, выполнена перезагрузка.

## Возврат к графическому режиму

![Переключение на graphical.target](Screenshot_13.png){ width=70% }

Установлен `graphical.target` как режим по умолчанию.


# Итоги работы

## Вывод

Изучены механизмы управления службами и целями systemd,  
включая автозагрузку, зависимости, конфликты сервисов,  
изоляцию целей и изменение режима загрузки системы.
