На самом деле в самой системе Linux конфигурационных файлов нет. Поскольку ОС - это всего лишь набор программ и ядро, то все эти файлы были созданы определенными программами и читаются ими же для настройки поведения. Большинство файлов, которые мы привыкли считать стандартными, относятся к системе инициализации или к другим системным утилитам.

Большинство файлов размещено в `/etc`. Название этой папки расшифровывается как “`etcetera`”, что с латинского означает “и другие” или “и так далее”.

1. **/etc/adjtime**: Файл с данными о времени
    1. `sudo cat /etc/adjtime`: Просмотр файла
    2. `sudo hwclock --systohc`: Сихронизация системного времени
2. **/etc/bash.bashrc**: Скрипт, который выполняется для каждого bash-сеанса
    1. `sudo echo "export CUSTOM_VAR='value'" >> /etc/bash.bashrc`: Добавление переменной среды в файл
    2. `source /etc/bash.bashrc`: Обновление текущего bash-сеанса
3. **/etc/crontab**: Файл с расписанием задач cron
    1. `sudo nano /etc/crontab`: Редактирование файла
    2. `sudo crontab -l`: Вывод текущих cron-задач для пользователя
4. /**etc/environment**: Файл для глобальных переменных
    1. `sudo echo "NEW_VARIABLE=value" >> /etc/environment`: Добавение переменной среды
    2. `sudo cat /etc/environment`: Просмотр файла
5. **/etc/fstab**: Файл правил монтирования файловых систем
    1. `sudo nano /etc/fstab`: Редактирование правил монтирования
    2. `sudo mount -a`: Монтировать все файловые системы из /etc/fstab
6. **/etc/group**: Файл, содержащий список групп пользователей
    1. `sudo cat /etc/group`: Просмотр групп и пользователей
    2. `sudo usermod -aG groupname username`: Добавление пользователя в группу
7. /**etc/hostname**: Файл с именем хоста системы
    1. `sudo cat /etc/hostname`: Просмотр имени хоста
    2. `sudo hostnamectl set-hostname newhostname`: Изменение имени хоста
8. **/etc/hosts**: Файл для сопоставления IP-адресов и доменных имен
    1. `sudo sh -c 'echo "127.0.0.2 [example.com](<http://example.com/>)" >> /etc/hosts'`: Добавление записи
    2. `sudo sed -i '/example.com/d' /etc/hosts`: Удаление записи
9. **/etc/hosts.allow и /etc/hosts.deny**: Файлы для управления доступом к сервисам
    1. `sudo sh -c 'echo "sshd: 192.168.1.0/24" >> /etc/hosts.allow’`: Разрешить доступ SHH для подсети
    2. `sudo sh -c 'echo "ALL: ALL" >> /etc/hosts.deny’`: Запретить доступ всем сервисам
10. **/etc/issue и /etc/issue.net**: Сообщения перед авторизациями
    1. `sudo sh -c 'echo "Welcome to our server" > /etc/issue’`: Сообщение перед локальной авторизацией
    2. `sudo sh -c 'echo "Authorized access only" > /etc/issue.net’`: Сообщение перед удаленной авторизацией
11. **/etc/ld.so.conf**: Пути к библиотекам
    1. `sudo sh -c 'echo "/usr/local/lib" >> /etc/ld.so.conf’`: Добавление пути
    2. `sudo sed -i '/\\/usr\\/local\\/lib/d' /etc/ld.so.conf`: Удаление пути
12. **/etc/localtime**: Файл с текущей зоной времени
    1. `sudo ln -sf /usr/share/zoneinfo/Zone/SubZone /etc/localtime`: Установка зоны времени
13. /**etc/login.defs**: Файл с политиками безопасности для логинов
    1. `sudo sed -i 's/^PASS_MAX_DAYS.*/PASS_MAX_DAYS 90/' /etc/login.defs`: Изменение максимального срока действия пароля
14. **/etc/mime.types**: Файл, который сопоставляет расширения файлов с MIME-типами
    1. `sudo sh -c 'echo "application/x-custom-type custom" >> /etc/mime.types’`: Добавление нового MIME-типа
    2. `sudo sed -i '/^application\\/x-custom-type/d' /etc/mime.types`: Удаление MIME-типа
15. **/etc/modprobe.d/**: Файлы конфигураций для управления модулями ядра
    1. `sudo touch /etc/modprobe.d/custom.conf`: Создание файла
16. /**etc/modules-load.d/**: Модули для автозагрузки при запуске системы
    1. `sudo touch /etc/modules-load.d/custom.conf`: Создание файла
17. **/etc/nsswitch.conf**: Файл, который определяет порядок разрешения имен
    1. `sudo sed -i 's/^passwd:.*$/passwd: files ldap/' /etc/nsswitch.conf`: Изменение порядка поиска для пользователей
18. **/etc/ntp.conf**: Конфигурация NTP-сервера
    1. `sudo sh -c 'echo "server [ntp.example.com](<http://ntp.example.com/>)" >> /etc/ntp.conf'`: Добавление NTP-сервера
19. **/etc/os-release**: Файл с информацией о версии ОС
    1. `sudo sed -i 's/^VERSION_ID=.*/VERSION_ID="20.04"/' /etc/os-release`: Изменение версии ОС
20. /**etc/passwd**: Файл со списком пользователей системы
    1. `sudo useradd -m newuser`: Создание нового пользователя
21. **/etc/motd**: Файл с сообщением при входе в систему
    1. `sudo nano /etc/motd`: Редактирование сообщения
22. **/etc/profile**: Файл с настройками среды для всех пользователей
    1. `sudo sh -c 'echo "export CUSTOM_VAR=value" >> /etc/profile’`: Добавление переменной среды для всех пользователей
23. **/etc/resolv.conf**: Настройки DNS-серверов
    1. `sudo sh -c 'echo "nameserver 8.8.8.8" > /etc/resolv.conf’`: Добавление DNS-сервера
24. **/etc/sddm.conf**: Файл с конфигурацией для SDDM
    1. `sudo cp /usr/share/sddm/scripts/Xsetup /etc/sddm.conf`: Копирование конфигурации
25. **/etc/shadow**: Файл с зашифрованными паролями пользователей
    1. `sudo useradd -m newuser`: Создание пользователя
    2. `sudo passwd newuser`: Установка пароля пользователю
26. /**etc/sudoers**: Файл для настройки прав пользователей через sudo
    1. `sudo sh -c 'echo "username ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers’`: Разрешение пользователю выполнять команды без пароля
27. **/etc/sysctl.conf**: Файл настроек параметров ядра
    1. `sudo sh -c 'echo "kernel.sysrq = 0" >> /etc/sysctl.conf’`: Отключение системных запросов
28. **/etc/vconsole.conf**: Файл с настройками консоли
    1. `sudo sh -c 'echo "KEYMAP=us" > /etc/vconsole.conf’`: Установка раскладки клавиатуры
29. **/etc/services**: Файл соответствия портов и служб
    1. `sudo sh -c 'echo "newservice 12345/tcp" >> /etc/services’`: Добавление новой службы
30. **/boot/grub/grub.cfg**: Файл с настройками загрузчика GRUB
    1. этот файл лучше не редактировать напрямую, поскольку он может быть перезаписан при обновлении GRUB. Вместо этого, настройки GRUB лучше изменять через файлы конфигурации в `/etc/default/grub` - `sudo sed -i 's/GRUB_DEFAULT=0/GRUB_DEFAULT=saved/' /etc/default/grub, sudo sed -i 's/#GRUB_TERMINAL_OUTPUT=console/GRUB_TERMINAL_OUTPUT=console/' /etc/default/grub, sudo update-grub`

### Конфигурация сети

```bash
# узнать какие интерфейсы используются в системе
dmesg | grep eth

# узнать иформацию о сетевом контроллере
lspci | grep Ethernet

# данный файл хранит имена и адреса локальной и других сетей. Является основным файлом для настроек сетевых интерфейсов
nano /etc/network/interfaces

# данный файл хранит перечень IP адресов и имена хостов
nano /etc/hosts

# этот файл определяет параметры преобразования сетевых имен в IP адреса
nano /etc/resolv.conf

# это утилита для конфигурации сети
nano /etc/netplan/01-netcfg.yaml

# показать адреса интерфейсов
ip a

# посмотреть статистику использования трафика сетевым интерфейсом
ip -s -h a

# мониторинг всех событий сетевых интерфейсов
ip monitor
```
