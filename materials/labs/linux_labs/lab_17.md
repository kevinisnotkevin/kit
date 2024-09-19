# LAB: iptables

0. Установить SSH-сервер (сменить стандартный порт на любой другой (сделать скриншот терминала с именем пользователя файла конфигурации))
1. Установить IpTables.
2. Изучить работу и правила по методическому указанию, которое представлено ниже.
3. Запретить ping – 2 способами (с возможностью записи событий в файл).
4. Запретить ssh соединения (с возможностью записи событий в файл).
5. Запретить все соединения (с возможностью записи событий в файл).
6. Запретить все соединения кроме ssh (с возможностью записи событий в файл).
7. Запретить все соединения только по одному ip-адресу (с возможностью записи событий в файл).
8. Запретить все соединения по одному ip-адресу, кроме ssh, подключиться по ssh (с возможностью записи событий в файл).
9. Запретить диапазон ip-адресов по маске 255.255.255.0 (с возможностью записи событий в файл).
10. Запретить диапазон портов 3000-4000 в сети 255.255.0.0 (с возможностью записи событий в файл).
11. Пробросить диапазон портов 1000-5000 на ip-адрес второй машины linux (с возможностью записи событий в файл).
12. Создать два собственных правила исходя из потребностей рабочего процесса или домашней локальной сети.