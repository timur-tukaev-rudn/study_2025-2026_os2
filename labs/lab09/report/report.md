---
## Front matter
title: "Отчёт по лабораторной работе №9"
subtitle: "Управление SELinux"
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

Получить навыки работы с контекстом безопасности и политиками SELinux.

# Выполнение

## Управление режимами SELinux

1. Получены права администратора с помощью команды `su`.  
   После ввода пароля произведён переход в контекст суперпользователя.  

   ![Переход в режим суперпользователя и просмотр состояния SELinux](Screenshot_1.png){ #fig:001 width=80% }

   Команда `sestatus -v` показала, что SELinux включён (enabled) и работает в режиме принудительного контроля доступа (enforcing).  
   Из вывода видно, что используется политика *targeted*, конфигурация загружена из файла `/etc/selinux/config`, а все процессы и файлы имеют назначенные контексты безопасности.

2. Проверено текущее состояние SELinux командой `getenforce`.  
   Система находится в режиме Enforcing.

3. Режим SELinux изменён на разрешающий (Permissive) с помощью `setenforce 0`.  
   После проверки через `getenforce` подтверждено, что режим изменился.  
   В этом режиме SELinux не блокирует действия, но фиксирует их в логах.

4. Открыт файл `/etc/sysconfig/selinux` и изменено значение параметра `SELINUX` на `disabled`, что полностью отключает SELinux после перезагрузки.

   ![Редактирование файла конфигурации SELinux](Screenshot_2.png){ #fig:002 width=80% }

5. После перезагрузки системы и повторного входа с правами администратора проверено состояние SELinux командой `getenforce`.  
   Вывод Disabled подтверждает, что SELinux отключён.

   ![Проверка отключённого состояния SELinux](Screenshot_3.png){ #fig:003 width=80% }

6. Попытка включить SELinux без перезагрузки (`setenforce 1`) завершилась сообщением «SELinux is disabled».  
   Это подтверждает, что смена режима невозможна при полном отключении механизма.

7. В файле `/etc/sysconfig/selinux` параметр изменён обратно на `SELINUX=enforcing`.

   ![Повторное включение SELinux через конфигурационный файл](Screenshot_4.png){ #fig:004 width=80% }

8. После перезагрузки система выдала предупреждение о необходимости восстановления меток безопасности (relabeling) для корректного применения политик SELinux.

   ![Сообщение о восстановлении меток безопасности при загрузке](Screenshot_5.png){ #fig:005 width=80% }

9. После завершения relabeling и загрузки ОС повторно выполнена проверка состояния SELinux через `sestatus -v`.  
   Система снова работает в режиме enforcing.

   ![Подтверждение восстановления работы SELinux в режиме enforcing](Screenshot_6.png){ #fig:006 width=80% }

## Использование restorecon для восстановления контекста безопасности

1. Проверен контекст безопасности файла `/etc/hosts` с помощью `ls -Z /etc/hosts`.  
   Файл имеет контекст `net_conf_t`.

2. Файл скопирован в домашний каталог командой `cp /etc/hosts ~/`.  
   При копировании контекст изменился на `admin_home_t`, так как файл создан в пользовательской области.

3. При попытке заменить оригинальный файл `/etc/hosts` командой `mv ~/hosts /etc` контекст остался `admin_home_t`, что не соответствует системному стандарту.

4. Для восстановления корректного контекста использована команда `restorecon -v /etc/hosts`.  
   Она автоматически вернула файлу правильный контекст `net_conf_t`.

5. Проверка подтвердила успешное восстановление.  
   Для массового исправления контекстов безопасности создан файл `/.autorelabel`.  
   После перезагрузки система автоматически перемаркировала все файлы.

   ![Использование restorecon и autorelabel для восстановления контекстов безопасности](Screenshot_7.png){ #fig:007 width=80% }

## Настройка контекста безопасности для нестандартного расположения файлов веб-сервера

1. Получены права администратора с помощью команды `su`.  
   Установлены необходимые пакеты `httpd` и `lynx` для запуска веб-сервера и проверки его работы в текстовом браузере.

2. Создан новый каталог `/web` для хранения файлов веб-сервера.  
   В нём создан файл `index.html` с содержимым:


![Создание каталога и файла веб-страницы](Screenshot_8.png){ #fig:008 width=80% }

3. В файле `/etc/httpd/conf/httpd.conf` закомментирована строка `DocumentRoot "/var/www/html"` и добавлена новая — `DocumentRoot "/web"`.  
Также изменён раздел `<Directory>` для указания новых прав доступа.

![Редактирование конфигурационного файла httpd.conf](Screenshot_9.png){ #fig:009 width=80% }

4. Служба `httpd` запущена и добавлена в автозагрузку.  
После запуска при обращении к серверу через `lynx http://localhost` открылась стандартная тестовая страница Rocky Linux, что говорит о том, что контекст безопасности SELinux пока не позволяет серверу обращаться к новому каталогу `/web`.

![Отображение стандартной страницы Rocky Linux](Screenshot_10.png){ #fig:010 width=80% }

5. Для корректного доступа веб-сервера к каталогу `/web` назначен соответствующий контекст безопасности SELinux:  
использована команда `semanage fcontext -a -t httpd_sys_content_t "/web(/.*)?"`,  
а затем выполнено восстановление контекста `restorecon -R -v /web`.

После изменения контекста система сообщила, что файлы были успешно перелабелированы.

![Применение контекста безопасности к каталогу /web](Screenshot_11.png){ #fig:011 width=80% }

6. При повторном обращении к серверу через браузер `lynx` отображается созданная страница с текстом «Welcome to my web-server», что подтверждает правильную настройку SELinux для нестандартного каталога веб-контента.

![Проверка работы веб-сервера с новым контентом](Screenshot_12.png){ #fig:012 width=80% }

## Работа с переключателями SELinux

1. Получены права администратора и просмотрены текущие параметры SELinux для служб, связанных с FTP, с помощью команды `getsebool -a | grep ftp`.  
Определено, что переключатель `ftpd_anon_write` имеет состояние `off`.

2. С помощью команды `semanage boolean -l | grep ftpd_anon` получена информация о назначении данного переключателя — он отвечает за разрешение анонимной записи через FTP.

3. Временное включение параметра выполнено командой `setsebool ftpd_anon_write on`.  
После проверки через `getsebool ftpd_anon_write` его значение изменилось на `on`.

4. Для сохранения изменения после перезагрузки параметр был включён постоянно командой `setsebool -P ftpd_anon_write on`.  
Повторная проверка показала, что теперь флаг `ftpd_anon_write` включён как во временном, так и в постоянном состоянии.

![Настройка переключателя SELinux для службы FTP](Screenshot_13.png){ #fig:013 width=80% }

# Контрольные вопросы

1. Для временного перевода SELinux в разрешающий режим используется команда `setenforce 0`.  

2. Для просмотра списка всех доступных переключателей SELinux применяется команда `getsebool -a`.  

3. Для получения человекочитаемых сообщений журнала SELinux необходимо установить пакет `setroubleshoot`.  

4. Для применения типа контекста `httpd_sys_content_t` к каталогу `/web` выполняются команды:  
   `semanage fcontext -a -t httpd_sys_content_t "/web(/.*)?"`  
   и  
   `restorecon -R -v /web`.  

5. Чтобы полностью отключить SELinux, необходимо изменить параметр `SELINUX=disabled` в файле `/etc/sysconfig/selinux`.  

6. SELinux регистрирует свои сообщения в файле `/var/log/audit/audit.log`.  

7. Для получения информации о доступных типах контекстов для службы FTP используется команда `semanage fcontext -l | grep ftp`.  

8. Чтобы определить, связано ли поведение сервиса с SELinux, можно воспользоваться утилитой `setroubleshoot` или командой `sealert -a /var/log/audit/audit.log`, которая анализирует журнал аудита и выдаёт рекомендации.  

# Заключение

В ходе лабораторной работы были изучены механизмы управления системой безопасности SELinux и её взаимодействие с сервисами Linux.  
Были рассмотрены режимы работы SELinux — принудительный (Enforcing), разрешающий (Permissive) и отключённый (Disabled).  
В процессе выполнения лабораторных заданий освоены приёмы изменения режима работы SELinux, редактирования конфигурационного файла `/etc/sysconfig/selinux`, а также восстановления контекстов безопасности с помощью команд `restorecon` и `semanage`.  
Особое внимание уделено настройке контекстов безопасности для нестандартного расположения веб-контента и управлению переключателями SELinux.  
Полученные навыки позволяют администратору корректно конфигурировать систему доступа и обеспечивать безопасную работу сервисов в среде Linux.  
