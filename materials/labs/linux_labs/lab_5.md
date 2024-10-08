# Lab 5

# Работа в командной строке

## Часть 1

1. Посмотреть, под каким пользователем в системе Linux
2. Перейти в корневой каталог
3. Посмотреть содержимое каталога
4. Определить типы команд `ls`, `cd`
5. Посмотреть из корневого каталога содержимое директории `Home`
6. Перейти в директорию `/tmp`
7. Создать директорию `pt` и перейти в нее
8. Создать 3 текстовых документа `1.txt` `2.txt` `3.txt`
9. Изменить или добавить содержимое текстового документа с помощью редакторов nano и vim _(1.txt отредактируйте с помощью nano, 2.txt – с помощью vim)_
10. Скопировать и переименовать директорию `pt` с файлами
11. Заархивировать переименованную директорию
12. Разархивировать переименованную директорию
13. Посмотреть содержимое домашней директории и каталога `usr` одной командой
14. Посмотреть содержимое каталога в обратном порядке
15. Посмотреть права на директорию
16. Посмотреть права на текстовые файлы
17. Переместить и переименовать один текстовый файл из директории `pt` в директорию `/tmp`
18. Создать новую директорию и перейти в нее; Создать новый текстовый документ; Переместить из директории `/tmp` файл в новую директорию с заменой файла и запросом на его изменение
19. Определить местоположение данного исполняемого файла `ls`, `cd`
20. Вывести краткое описание команды и утилиты `ls`, `nano`, `rm`
21. Создать символическую ссылку
22. Создать символическую ссылку в никуда, продемонстрировать её
23. Создать прямую ссылку
24. Создать собственную команду, которая будет использовать следующее: `cd /usr; ls; cd -,` с помощью `alias`
25. С помощью команды `cat` написать любой текст и записать его в текстовый файл
26. Создать скрипт - минимум 5 строк
27. Удалить один текстовый файл (_например, текстовый файл из пункта 1.25_)
28. Создать пустую директорию и удалить её
29. Удалить директорию с 3-я текстовыми файлами _(директория `pt`)_
30. Создать пользователя
31. Поменять пароль для нового пользователя
32. Найти текстовый документ с помощью команд Find и Grep _(нужно использовать и то, и то)_
33. Воспользоваться логическими “и” и “или”, как пример использовать команду ping _(должно быть два запроса)_

## Часть 2

1. Создать новый каталог для архивированных данных
2. Архивировать файлы с данными
3. Переместить архивированные данные в новый каталог
4. Создать скрипт для регулярного архивирования данных
5. Сделать скрипт исполняемым
6. Запустить скрипт для создания регулярного архива данных
7. Установить расписание для регулярного запуска скрипта
8. Создать скрипт для очистки старых архивов
9. Сделать скрипт исполняемым
10. Запустить скрипт для очистки старых архивов
11. Установить расписание для регулярной очистки старых архивов
12. Создать скрипт для резервного копирования базы данных
13. Сделать скрипт исполняемым
14. Запустить скрипт для создания резервной копии базы данных
15. Установить расписание для регулярного создания резервных копий базы данных
16. Создать скрипт для восстановления базы данных из резервной копии
17. Сделать скрипт исполняемым
18. Запустить скрипт для восстановления базы данных из резервной копии
19. Создать отчет об использовании дискового пространства
20. Добавить результат в общий отчет

Задача направлена на автоматизацию резервного копирования данных, архивирования и очистки старых данных, а также создания.

## Часть 3

1. Создать новую директорию для хранения данных
2. Создать файлы для каждой таблицы базы данных
3. Сгенерировать файлы с данными для каждой таблицы
4. Загрузить данные в каждый файл
5. Проверить содержимое файлов с данными
6. Создать скрипт для автоматической загрузки данных
7. Сделать скрипт исполняемым
8. Запустить скрипт для загрузки данных
9. Создать SQL-скрипт для создания таблиц в базе данных
10. Создать базу данных для хранения данных
11. Загрузить SQL-скрипт для создания таблиц
12. Создать SQL-скрипт для загрузки данных из файлов CSV в таблицы базы данных
13. Запустить SQL-скрипт для загрузки данных в базу данных
14. Проверить, что данные успешно загружены в базу данных
15. Создать SQL-скрипт для выполнения аналитики по базе данным (например, сумма прибыли компании за год, количество строк в таблице и тп)
16. Запустить SQL-скрипт для выполнения аналитики
17. Сохранить результаты аналитики в файл
18. Добавить в отчёт результат аналитики

В конечном итоге все эти шаги объединены в создание базы данных из файлов данных, загрузка данных в базу данных, аналитика данных и добавление результатов в отчёт.