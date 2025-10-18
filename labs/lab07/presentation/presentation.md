---
## Front matter
lang: ru-RU
title: Лабораторная работа №7
subtitle: Управление журналами событий в системе
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

Получить навыки работы с журналами мониторинга различных событий в системе Linux,  
освоить использование служб `rsyslogd` и `journald`, а также методы фильтрации и анализа сообщений.

# Ход выполнения работы

## Мониторинг системных событий

![Мониторинг системных сообщений](Screenshot_1.png){ #fig:001 width=70% height=70% }

Запуск `tail -f /var/log/messages` для просмотра событий ядра и служб в реальном времени.

## Ошибка аутентификации root

![Ошибка при вводе пароля root](Screenshot_2.png){ #fig:002 width=70% height=70% }

Попытка входа с неверным паролем приводит к записи `FAILED SU (to root)` в системный журнал.

## Отправка пользовательского сообщения

![Регистрация пользовательского сообщения](Screenshot_3.png){ #fig:003 width=70% height=70% }

Команда `logger hello` добавляет сообщение в `/var/log/messages`.

## Настройка rsyslog для Apache

![Настройка передачи логов Apache в rsyslog](Screenshot_4.png){ #fig:004 width=70% height=70% }

Добавлена строка `ErrorLog syslog:local1` в `/etc/httpd/conf/httpd.conf`.

## Создание отдельного файла для логов Apache

![Создание конфигурации rsyslog](Screenshot_5.png){ #fig:005 width=70% height=70% }

Создан файл `/etc/rsyslog.d/httpd.conf` с правилом `local1.* -/var/log/httpd-error.log`.

## Настройка отладочного журнала

![Создание debug.conf для отладки](Screenshot_6.png){ #fig:006 width=70% height=70% }

Добавлено правило `*.debug /var/log/messages-debug` для регистрации отладочной информации.

## Проверка работы rsyslog

![Регистрация отладочного сообщения](Screenshot_7.png){ #fig:007 width=70% height=70% }

Сообщение `Daemon Debug Message` успешно зафиксировано в `/var/log/messages-debug`.

## Использование journalctl

![Просмотр журнала через journalctl](Screenshot_10.png){ #fig:008 width=70% height=70% }

Просмотр системных сообщений, фильтрация по UID и модулю `sshd.service`.

## Постоянный журнал journald

![Создание каталога для постоянного хранения журнала](Screenshot_17.png){ #fig:009 width=70% height=70% }

Создан каталог `/var/log/journal` и настроены права для обеспечения постоянного хранения логов.

# Итоги работы

## Вывод

В результате лабораторной работы изучены инструменты системного журналирования Linux.  
Настроено взаимодействие служб `rsyslogd` и `journald`, реализовано хранение логов в постоянном режиме.  
Получены навыки фильтрации, анализа и управления системными событиями.
