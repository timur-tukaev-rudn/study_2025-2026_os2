---
## Front matter
title: "Отчёт по лабораторной работе №15"
subtitle: "Управление логическими томами"
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

Получить навыки управления логическими томами.

# Выполнение

## Создание физического тома

1. В файле `/etc/fstab` строки автомонтирования `/mnt/data` и `/mnt/data-ext` были закомментированы. Затем выполнено их отмонтирование.

2. Командой `mount` без параметров подтверждено отсутствие смонтированных томов `/dev/sdb` и `/dev/sdc`.

3. В `fdisk /dev/sdb` создана новая таблица разделов DOS (`o`), после проверки (`p`) изменения записаны (`w`).

4. Обновление таблицы разделов выполнено командой `partprobe /dev/sdb`.

5. Информация о разделах просмотрена через `cat /proc/partitions` и `fdisk --list /dev/sdb`.

6. Создан основной раздел на диске `/dev/sdb` размером 300 МБ, изменён тип на `8e`, затем создан физический том `pvcreate /dev/sdb1`.

![Разметка диска /dev/sdb](Screenshot_1.png){ #fig:001 width=80% }

![Создание PV](Screenshot_2.png){ #fig:002 width=80% }

## Создание группы томов и логических томов

1. Командой `pvs` подтверждено наличие физического тома `/dev/sdb1`.

2. Создана группа томов:
   `vgcreate vgdata /dev/sdb1`.

3. Проверена успешность создания команды `vgs` и повторным `pvs`.

![Создание VG](Screenshot_3.png){ #fig:003 width=80% }

4. Создан логический том:
   `lvcreate -n lvdata -l 50%FREE vgdata`.

5. Создана файловая система ext4 на `/dev/vgdata/lvdata`.

6. Создан каталог `/mnt/data`.

7. В файл `/etc/fstab` добавлена строка для автомонтирования тома.

![fstab](Screenshot_5.png){ #fig:005 width=80% }

8. Проверено корректное монтирование (`mount -a`, `mount | grep /mnt`).

![Монтирование](Screenshot_6.png){ #fig:006 width=80% }

## Изменение размера логических томов

1. Состояние томов проверено командами `pvs` и `vgs`.

2. На диске `/dev/sdb` создан дополнительный раздел `/dev/sdb2` размером 300 МБ с типом `8e`.

![Создание раздела sdb2](Screenshot_7.png){ #fig:007 width=80% }

3. Создан физический том `/dev/sdb2`.

4. Группа томов расширена:
   `vgextend vgdata /dev/sdb2`.

5. Проверена увеличенная ёмкость группы томов.

6. Проверены размеры логического тома (`lvs`) и файловой системы (`df -h`).

7. Логический том увеличен:
   `lvextend -r -l +50%FREE /dev/vgdata/lvdata`.

![Увеличение тома](Screenshot_8.png){ #fig:008 width=80% }

8. Подтверждено появление дополнительного пространства.

9. Размер тома уменьшен на 50 МБ:
   `lvreduce -r -L -50M /dev/vgdata/lvdata`.

10. Выполнена финальная проверка томов и файловой системы.

![Уменьшение размера тома](Screenshot_9.png){ #fig:009 width=80% }

## Самостоятельная работа

### 1. Создание логического тома lvgroup, форматирование и монтирование

1. На диске `/dev/sdc` создано два раздела: `/dev/sdc1` и `/dev/sdc2`, оба с типом `8e` (Linux LVM).  
   Это подтверждается выводом `fdisk`:

![Разметка диска /dev/sdc](Screenshot_10.png){ #fig:010 width=80% }

2. Создан физический том на `/dev/sdc1`:
   `pvcreate /dev/sdc1`

3. Создана группа томов:
   `vgcreate vvgroup /dev/sdc1`

4. Создан логический том размером 100%FREE (≈200 МБ):
   `lvcreate -n lvgroup -l 100%FREE vvgroup`

5. На логическом томе создана файловая система XFS:
   `mkfs.xfs /dev/vvgroup/lvgroup`

6. Создан каталог монтирования `/mnt/groups`.

7. В файл `/etc/fstab` добавлена строка постоянного монтирования:

![fstab с записью для /mnt/groups](Screenshot_12.png){ #fig:012 width=80% }

8. Проверено монтирование и вывод `df -h`, устройство успешно подключено:

![Монтирование /mnt/groups](Screenshot_13.png){ #fig:013 width=80% }

### 2. Увеличение логического тома lvgroup на 150 МБ

1. Создан физический том на `/dev/sdc2`:
`pvcreate /dev/sdc2`

2. Группа томов расширена:
`vgextend vvgroup /dev/sdc2`

3. Логический том увеличен на 100%FREE (≈150 МБ):
`lvextend -r -l +100%FREE /dev/vvgroup/lvgroup`

Расширение файловой системы XFS выполнено автоматически (`xfs_growfs`).

![Расширение тома lvgroup](Screenshot_14.png){ #fig:014 width=80% }

### 3. Проверка успешного расширения

1. Команда `pvs` показывает новое доступное пространство физического тома.

2. Команда `vgs` подтверждает увеличение размера группы томов.

3. `lvs` позволяет увидеть новый размер логического тома.

4. `df -h` демонстрирует увеличенный размер файловой системы XFS на `/mnt/groups`.

![Проверка после расширения](Screenshot_15.png){ #fig:015 width=80% }

# Контрольные вопросы

1. Для работы LVM в таблице разделов GUID используется тип раздела **0x8e00 (Linux LVM)**.

2. Создать группу томов `vggroup`, включающую устройство `/dev/sdb3` и использующую физический экстент 4 MiB, можно командой:
   `vgcreate -s 4M vggroup /dev/sdb3`.

3. Краткую сводку физических томов и их принадлежности к группам томов показывает команда:
   `pvs`.

4. Чтобы добавить весь диск `/dev/sdd` в группу томов, необходимо:
   – создать физический том: `pvcreate /dev/sdd`;
   – затем расширить группу томов: `vgextend <имя_группы> /dev/sdd`.

5. Создать логический том `lvvol1` размером 6 MiB можно командой:
   `lvcreate -n lvvol1 -L 6M <имя_группы>`.

6. Добавить 100 МБ в логический том `lvvol1` можно командой:
   `lvextend -L +100M /dev/<имя_группы>/lvvol1`.

7. Если в группе томов недостаточно свободного места, первый шаг — **добавить новый физический том** с помощью:
   `pvcreate <устройство>`,
   а затем расширить группу томов: `vgextend <имя_группы> <устройство>`.

8. Чтобы вместе с увеличением тома расширить файловую систему, в `lvextend` используют опцию:
   `-r` (resize filesystem).

9. Просмотреть доступные логические тома можно командой:
   `lvs`.

10. Проверить целостность файловой системы на `/dev/vgdata/lvdata` можно командой:
    `fsck /dev/vgdata/lvdata`.


# Заключение

В ходе работы были выполнены операции по созданию, расширению и управлению логическими томами LVM. Освоены приёмы разметки дисков, формирования физических и групповых томов, создания файловых систем и их динамического изменения. Практические действия подтвердили гибкость LVM и его удобство для масштабирования хранилища.
