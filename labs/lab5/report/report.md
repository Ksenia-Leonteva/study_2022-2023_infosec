---
## Front matter
title: "Лабораторная работа №5"
subtitle: "Информационная безопасность"
author: "Леонтьева Ксения Андреевна | НПМбд-01-19"

## Generic otions
lang: ru-RU
toc-title: "Содержание"

## Bibliography
bibliography: bib/cite.bib
csl: pandoc/csl/gost-r-7-0-5-2008-numeric.csl

## Pdf output format
toc: true # Table of contents
toc-depth: 2
lof: true # List of figures
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
mainfont: PT Serif
romanfont: PT Serif
sansfont: PT Sans
monofont: PT Mono
mainfontoptions: Ligatures=TeX
romanfontoptions: Ligatures=TeX
sansfontoptions: Ligatures=TeX,Scale=MatchLowercase
monofontoptions: Scale=MatchLowercase,Scale=0.9
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
  - \usepackage{float} # keep figures where there are in the text
  - \floatplacement{figure}{H} # keep figures where there are in the text
---

# Цель работы

Изучение механизмов изменения идентификаторов, применения SetUID- и Sticky-битов. Получение практических навыков работы в консоли с дополнительными атрибутами. Рассмотрение работы механизма смены идентификатора процессов пользователей, а также влияние бита Sticky на запись и удаление файлов.

# Теоретическое введение

SetUID, SetGID и Sticky - это специальные типы разрешений позволяют задавать расширенные права доступа на файлы или каталоги.

* SetUID (set user ID upon execution — «установка ID пользователя во время выполнения) являются флагами прав доступа в Unix, которые разрешают пользователям запускать исполняемые файлы с правами владельца исполняемого файла. 

* SetGID (set group ID upon execution — «установка ID группы во время выполнения») являются флагами прав доступа в Unix, которые разрешают пользователям запускать исполняемые файлы с правами группы исполняемого файла.

* Sticky bit в основном используется в общих каталогах, таких как /var или /tmp, поскольку пользователи могут создавать файлы, читать и выполнять их, принадлежащие другим пользователям, но не могут удалять файлы, принадлежащие другим пользователям. 

Более подробно см. в [@prava:bash].

# Выполнение лабораторной работы

## Создание программы

Для начала я убедилась, что компилятор gcc установлен, исолпьзуя команду "gcc -v". Затем отключила систему запретов до очередной перезагрузки системы командой "sudo setenforce 0", после чего команда "getenforce" вывела "Permissive" (рис. [-@fig:001]).
 
![Предварительная подготовка](image/Pic 1.jpg){ #fig:001 width=110% }

Проверила успешное выполнение команд "whereis gcc" и "whereis g++" (их расположение) (рис. [-@fig:002]).

![Команда "whereis"](image/Pic 2.jpg){ #fig:002 width=110% }

Вошла в систему от имени пользователя guest командой "su - guest". Создала программу simpleid.c командой "touch simpleid.c" и открыла её в редакторе командой "gedit /home/guest/simpleid.c" (рис. [-@fig:004]).

![Вход в систему и создание программы](image/Pic 4.jpg){ #fig:004 width=110% }

Код программы выглядит следующим образом (рис. [-@fig:003]).

![Код программы simpleid.c](image/Pic 3.jpg){ #fig:003 width=110% }

Скомпилировала программу и убедилась, что файл программы был создан командой "gcc simpleid.c -o simpleid". Выполнила программу simpleid командой "./simpleid", а затем выполнила системную программу id командой "id". Результаты, полученные в результате выполнения обеих команд, совпадают (uid=1001 и gid=1001) (рис. [-@fig:005]).

![Компиляция и выполнение программы simpleid](image/Pic 5.jpg){ #fig:005 width=110% }

Усложнила программу, добавив вывод действительных идентификаторов (рис. [-@fig:006]).

![Усложнение программы](image/Pic 6.jpg){ #fig:006 width=110% }

Получившуюся программу назвала simpleid2.c (рис. [-@fig:008]).

![Переименование программы в simpleid2.c](image/Pic 8.jpg){ #fig:008 width=110% }

Скомпилировала и запустила simpleid2.c командами "gcc simpleid2.c -o sipleid2" и "./simpleid2" (рис. [-@fig:009]).

![Компиляция и выполнение программы simpleid2](image/Pic 9.jpg){ #fig:009 width=110% }

От имени суперпользователя выполнила команды "sudo chown root:guest /home/guest/simpleid2" и "sudo chmod u+s /home/guest/simpleid2", затем выполнила проверку правильности установки новых атрибутов и смены владельца файла simpleid2 командой "sudo ls -l /home/guest/simpleid2" (рис. [-@fig:010]). Этими командами была произведена смена пользователя файла на root и установлен SetUID-бит.

![Установка новых атрибутов (SetUID) и смена владельца файла](image/Pic 10.jpg){ #fig:010 width=110% }

Запустила программы simpleid2 и id. Теперь появились различия в uid (рис. [-@fig:019]).

![Запуск simpleid2 после установки SetUID](image/Pic 19.jpg){ #fig:019 width=110% }

Проделала тоже самое относительно SetGID-бита. Также можем заметить различия с предыдущим пунктом (рис. [-@fig:011]).

![Запуск simpleid2 после установки SetGID](image/Pic 11.jpg){ #fig:011 width=110% }

Создаем программу readfile.c (рис. [-@fig:012]).

![Код программы readfile.c](image/Pic 12.jpg){ #fig:012 width=110% }

Скомпилировала созданную программу командой "gcc readfile.c -o readfile". Сменила владельца у файла readfile.c командой "sudo chown root:guest /home/guest/readfile.c" и поменяла права так, чтобы только суперпользователь мог прочитать его, а guest не мог, с помощью команды "sudo chmod 700 /home/guest/readfile.c". Теперь убедилась, что пользователь guest не может прочитать файл readfile.c командой "cat readfile.c", получив отказ в доступе (рис. [-@fig:013]).

![Смена владельца и прав доступа у файла readfile.c](image/Pic 13.jpg){ #fig:013 width=110% }

Поменяла владельца у программы readfile и устанавила SetUID. Проверила, может ли программа readfile прочитать файл readfile.c командой "./readfile readfile.c". Прочитать удалось. Аналогично проверила, можно ли прочитать файл /etc/shadow. Прочитать удалось (рис. [-@fig:014]).

![Запуск программы readfile](image/Pic 14.jpg){ #fig:014 width=110% }

## Исследование Sticky-бита

Командой "ls -l / | grep tmp" убеждилась, что атрибут Sticky на директории /tmp установлен. От имени пользователя guest создала файл file01.txt в директории /tmp со словом test командой "echo "test" > /tmp/file01.txt". Просматрела атрибуты у только что созданного файла и разрешаем чтение и запись для категории пользователей "все остальные" командами "ls -l /tmp/file01.txt" и "chmod o+rw /tmp/file01.txt" (рис. [-@fig:015]).

![Создание файла file01.txt](image/Pic 15.jpg){ #fig:015 width=110% }

От имени пользователя guest2 попробовала прочитать файл командой "cat /tmp/file01.txt" - это удалось. Далее попыталась дозаписать в файл слово test2, проверить содержимое файла и записать в файл слово test3, стерев при этом всю имеющуюся в файле информацию - эти операции удалось выполнить только в случае, если еще дополнительно разрешить чтение и запись для группы пользователей командой "chmod g+rw /tmp/file01.txt". От имени пользователя guest2 попробовала удалить файл - это не удается ни в каком из случаев, возникает ошибка (рис. [-@fig:016]).

![Попытка выполнить действия над файлом file01.txt от имени пользователя guest2](image/Pic 16.jpg){ #fig:016 width=110% }

Повысила права до суперпользователя командой "su -" и выполнила команду, снимающую атрибут t с директории /tmp "chmod -t /tmp". После чего покинула режим суперпользователя командой "exit". Повторила предыдущие шаги. Теперь мне удалось удалить файл file01.txt от имени пользователя, не являющегося его владельцем (рис. [-@fig:017]).

![Удаление атрибута t (Sticky-бита) и повторение действий](image/Pic 17.jpg){ #fig:017 width=110% }

Повысила свои права до суперпользователя и вернула атрибут t на директорию /tmp (рис. [-@fig:018]).

![Возвращение атрибута t (Sticky-бита)](image/Pic 18.jpg){ #fig:018 width=110% } 

# Выводы

В ходе выполнения данной лабораторной работы я изучила механизмы изменения идентификаторов, применение SetUID- и Sticky-битов. Получила практические навыки работы в консоли с дополнительными атрибутами. Рассмотрела работу механизма смены идентификатора процессов пользователей, а также влияние бита Sticky на запись и удаление файлов.

# Список литературы{.unnumbered}

::: {#refs}
:::


