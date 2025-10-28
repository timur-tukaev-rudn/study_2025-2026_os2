---
## Front matter
lang: ru-RU
title: Лабораторная работа №9
subtitle: Управление SELinux
author:
  - Тукаев Тимур
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 16 октября 2025

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

Получить навыки работы с контекстом безопасности и политиками SELinux.  
Освоить принципы конфигурирования, изменения режима работы и восстановления контекстов безопасности.

# Ход выполнения работы

## Проверка состояния SELinux

![Проверка состояния SELinux](Screenshot_1.png){ #fig:001 width=70% height=70% }

Команда `sestatus -v` показала, что система работает в режиме Enforcing.

## Изменение режима работы SELinux

![Изменение режима работы SELinux](Screenshot_2.png){ #fig:002 width=70% height=70% }

Режим переведён в Permissive с помощью `setenforce 0`, затем в Disabled через изменение файла конфигурации.

## Проверка после перезагрузки

![Проверка отключённого состояния SELinux](Screenshot_3.png){ #fig:003 width=70% height=70% }

SELinux отключён. Попытка включить без перезагрузки невозможна.

## Повторное включение SELinux

![Повторное включение SELinux](Screenshot_4.png){ #fig:004 width=70% height=70% }

Файл `/etc/sysconfig/selinux` изменён на `SELINUX=enforcing`.  
После перезагрузки выполнено relabeling файловой системы.

## Завершение relabeling

![Сообщение о восстановлении меток безопасности](Screenshot_5.png){ #fig:005 width=70% height=70% }

Система автоматически пересоздала метки контекста безопасности.  
SELinux работает в режиме Enforcing.

## Восстановление контекста безопасности

![Использование restorecon для восстановления контекста](Screenshot_6.png){ #fig:006 width=70% height=70% }

Использованы команды `restorecon` и `/.autorelabel` для восстановления контекстов безопасности системных файлов.

## Настройка контекста для веб-сервера

![Редактирование httpd.conf и настройка каталога /web](Screenshot_9.png){ #fig:009 width=70% height=70% }

Создан каталог `/web`, добавлен файл `index.html`.  
В конфигурации Apache изменён путь `DocumentRoot`.

## Применение нового контекста SELinux

![Применение контекста безопасности к /web](Screenshot_11.png){ #fig:011 width=70% height=70% }

К каталогу `/web` применён контекст `httpd_sys_content_t` и выполнено `restorecon -R -v /web`.

## Проверка доступа к веб-странице

![Проверка работы веб-сервера](Screenshot_12.png){ #fig:012 width=70% height=70% }

После применения контекста страница доступна по адресу `http://localhost`.

## Работа с переключателями SELinux

![Настройка переключателя ftpd_anon_write](Screenshot_13.png){ #fig:013 width=70% height=70% }

Просмотр, временное и постоянное включение переключателя `ftpd_anon_write` для FTP-службы.  
Параметр активирован (on).

# Итоги работы

## Вывод

Изучены режимы работы SELinux и практические методы их изменения.  
Освоены приёмы назначения и восстановления контекстов безопасности, настройки веб-сервера и управления переключателями SELinux.  
Полученные навыки обеспечивают эффективную настройку и защиту системы Linux.
