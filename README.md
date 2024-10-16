# Base it

# 🐧 Linux

- [Введение](materials/linux/introduction.md)
- [Ядро Linux](materials/linux/about.md)
- [**bash**](materials/linux/bash.md): Командная оболочка, скрипты
- [Полезные и опасные команды (fork-бомба)](materials/linux/fork.md)
- [Disks](materials/disks.md)
- [RAID](materials/linux/raid.md)
- [Администрирование Linux](materials/linux/admin.md)
- [**PAM**](materials/linux/pam.md): Подключаемые модули аутентификации
- [IPv6](materials/linux/ipv6.md)
- [MAC](materials/linux/mac.md)
- [Файлы конфигураций Linux](materials/linux/file_configs.md)
- [**vim**](materials/linux/vim.md): Текстовый редактор (Понять - vimtutor)

## 🎡 Лабы

- [Работа с sudo](materials/labs/linux/work_with_sudo.md)
- [Работа с sg](materials/labs/linux/work_with_sg.md)
- [Лаба: IPv6 и MAC-адреса](materials/labs/linux_labs/lab_16.md)
- [Лаба: SSH](materials/labs/ssh/lab_2.md)
- [Лаба: iptables](materials/labs/linux_labs/lab_17.md)
- [Лаба: sshprank](materials/labs/linux_labs/lab_18.md)
- [Лаба: pwdlogy & crunch](materials/labs/lab_pwdlogy_crunch.md)
- [Лаба: sshprank](materials/labs/lab_sshprank.md)
- [Лаба: Конфигурирование двух серверов](materials/labs/configuring2servers.md)

## 📒 Runbook

- [Проверка пароля на компрометацию](materials/runbook/check_pass.md)
- [GRUB и Сброс пароля root на примере Kali](materials/runbook/grub_root_kali.md)
- [Генерация вордлистов (pwdlogy, crunch)](materials/runbook/generate_wordlist.md)
- [Настройка VLAN](materials/runbook/vlan.md)
- [VLAN & DHCP](materials/runbook/vlan_dhcp.md)
- [Fail2Ban-SSH и Brute-force](materials/runbook/fail2ban_ssh_bruteforce.md)
- [Brute-force SSH с защитой Fail2Ban и без защиты](materials/runbook/bruteforce_ssh.md)
- [DoS на NGINX с Fail2Ban](materials/runbook/dos_nginx_fail2ban.md)

## 📆 Автоматизация задач

Планирование задач позволяет планировать и автоматизировать запуск задач в определенное время или с определенной частотой без необходимости запускать их вручную.

Примеры использования: автоматическое обновление программного обеспечения, запуск сценариев, очистка баз данных и автоматизация резервного копирования.

- [**CRON**](materials/linux/cron.md): Выполнение задач в определенное время

## 🧑‍💻 Права доступа

Обычные пользователи - Они создаются системным администратором. Каждый пользователь имеет числовой идентификатор пользователя, называемый UID

Системные пользователи - Системные пользователи создаются самой операционной системой. Скорее всего они используются какими-либо приложениями

Root - Данный пользователь имеет UID = 0 Root может выполнять все операции на низком уровне, запись в критическую область памяти и прочее Работать с root правами не рекомендуется

## 📦 Дистрибутивы

- Дистрибутив Linux - установочный пакет для развертывания ОС, состоящей из ядра Linux, утилит GNU, дополнительного ПО и диспетчера пакетов.	
	Он также может включать пакет для установки дисплейного сервера и развертывания среды рабочего стола. Таким образом, Linux - ядро ОС, а дистрибутив Linux - установочный пакет какой-то из разновидностей этой ОС + доп. компоненты. Такие разновидности называют ОС на базе Linux. Дистрибутивы Linux используются в серверах, встроенных устройствах, настольных ПК, мобильных устройствах, облачных вычислениях.

- [**Ubuntu**](materials/linux/ubuntu.md): Пользовательский дистрибутив с широким сообществом, удобный для новичков
- [**Debian**](materials/linux/debian.md): Стабильность и безопасность, часто используется на серверах
- [**Fedora**](materials/linux/fedora.md): Современные технологии и новейшее ПО, идеален для разработчиков
- [**CentOS**](materials/linux/centos.md): Корпоративный уровень стабильности, широко используется на серверах
- [**Kali Linux**](materials/linux/kali.md): Дистрибутив для тестирования на проникновение и информационной безопасности
- [**ParrotOS**](materials/linux/parrot.md): Дистрибутив для тестирования на проникновение, безопасности и цифровой криминалистики. Также включает инструменты для анонимности и защиты данных
- [**BlackArch**](materials/linux/blackarch.md): Расширение для Arch Linux, предназначенное для тестирования на проникновение. Включает огромное количество инструментов для проведения всестороннего анализа безопасности

## 🔑 Сервисы и службы

- [**systemd**](materials/linux/systemd.md): Инициализатор сервисов, первый процесс

- [**apache**](materials/linux/apache.md): Кроссплатформенное программное обеспечение для гибкой настройки надежных веб-серверов
- [**nginx**](materials/linux/nginx.md): Веб-сервер с открытым исходным кодом, созданный работать под высокой нагрузкой
- [**telnet**](materials/linux/telnet.md): Сетевой протокол для реализации текстового терминального интерфейса по сети
- [**ssh**](materials/linux/ssh.md): Протокол, обеспечивающий защищённые соединения и передачу данных между двумя удалёнными компьютерами
- [**ftp**](materials/linux/ftp.md): Протокол передачи файлов по сети
- [**smb**](materials/linux/smb.md): Программа, которая позволяет компьютерам UNIX/Linux имитировать машины Windows

## 🧰 Утилиты

### 💻 Система

- [__whoami__](#whoami): Имя текущего пользователя
- [__id__](#id): Членство пользователя в группах
- [__hostname__](#hostname): Устанавливает или печатает имя текущего хоста
- [__uname__](#uname): Основная информация об ОС и системном оборудовании
- [__pwd__](#pwd): Рабочая директория
- [**ip**](materials/linux/ip.md): Утилита для отображения и управления маршрутизацией, сетевыми устройствами, интерфейсами, туннелями
- [**netplan**](materials/linux/netplan.md): Управление конфигурацией сети
- [**ps**](materials/linux/ps.md): Утилита для мониторинга активных процессов
- [__who__](#who): Кто вошел в систему
- [__env__](#env): Устанавливает или печатает переменные среды
- **lsblk**: Утилита для вывода информации о блочных устройствах в системе
- **lsusb**: Утилита для вывода информации о USB устройствах
- **lsof**: Утилита для вывода информации об открытых файлах
- [__lspci__](#lspci): Список PCI устройств
- [**jobs**](#jobs): Все процессы, запущенные в фоновом режиме, вместе с их статусами
- [**lynis**](materials/linux/lynis.md): Утилита для тестирования сервера
- [**fail2ban**](materials/linux/fail2ban.md): Утилита для предотвращения взлома методом брутфорса

### 👱‍♂️ Пользователи

- [**sudo**](materials/linux/sudo.md): Утилита для выполнения команд от имени другого пользователя
- [**passwd**](materials/linux/passwd.md): Утилита для смены пароля пользователя
- [**useradd**](materials/linux/useradd.md): Утилита для создания пользователей
- **userdel**: Утилита для удаления пользователя
- **usermod**: Утилита для изменения пользователя
- **groupadd**: Утилита для создания группы
- **groupdel**: Утилита для удаления группы
- **groupmod**: Утилита для изменения настроек группы

### 📦 Пакеты

- [__apt__](materials/linux/apt.md): Высокоуровневый интерфейс командной строки для системы управления пакетами
- pip: Управление Python-пакетами
- **git**: Система контроля версий
- [**dpkg**](materials/linux/dpkg.md): Работа с DEB-пакетами

### 🧑‍💻 Мониторинг ОС

- **df**: Утилита для получения информации о файловой системе
- **du**: Утилита для определения размеров файлов в директории
- [__free__](#free): Информация об оперативной памяти
- [**iostat**](materials/linux/iostat.md): Утилита для мониторинга статистики ввода-вывода системы для устройств и разделов
- [__vmstat__](#vmstat): Статистика виртуальной памяти, дисках, ядрах, переключении контекста, работы процессора
- [__w__](#w): Список активных пользователей с момента запуска ОС
- [__htop__](#htop): Интерактивная информация о процессах, потреблении системных ресурсов
- [**auditd**](materials/linux/auditd.md): Утилита для мониторинга событий ОС и записи их в журнал событий

### 🧑‍💻 База

- [__date__](#date): Системная дата
- [__export__](#export): Экспорт переменных среды
- [**find**](materials/linux/find.md): Утилита для поиска файлов и директорий по фильтрам
- [__gunzip__](#gunzip): Распаковка gzip архивов
- [__gzip__](#gzip): Сжатие файлов алгоритмом LZ77
- [__history__](#history): История введенных команд
- [__locate__](#locate): Поиск файлов и папок
- [**man**](materials/linux/man.md): Утилита для получения справочной информации нужной команды
- [**mount**](materials/linux/mount.md): Утилита для монтирования файловой системы
- [__reboot__](#reboot): Перезагрузка системы
- [__service__](#service): Управление работой сервисов
- [__shutdown__](#shutdown): Остановка системы
- [**tar**](materials/linux/tar.md): Утилита для сжатия и распаковки файлов
- [__uptime__](#uptime): Время работы в системы
- **wget**: Утилита для загрузки файлов с FTP или HTTP-серверов из терминала. Аналог curl
- [__whatis__](#whatis): Краткое описание искомой команды
- [__which__](#which): Поиск расположения исполняемого файла
- [**zip**](materials/linux/zip.md): Утилита для zip упаковки файлов
- [**chattr**](materials/linux/chattr.md): Утилита для управления специальными атрибутами файлов для предотвращения случайной модификации и удаления
- [**sg**](materials/linux/sg.md): Утилита для выполнения команд с правами определенной группы

### 🕸️ Фильтры

- **head**: Первые строки файла
- **less**: Постраничный просмотр файлов
- **more**: Постраничный просмотр файлов
- **tail**: Последние строки файла
- **sort**: Сортировка строк файлов
- [**grep**](materials/linux/grep.md): Утилита для поиска строк текста
- **cut**: Вырезание разделов из каждой строки файлов
- [__tr__](#tr): Перевод или удаление символов
- [__column__](#column): Отображение в табличной форме
- [**awk**](materials/linux/awk.md): Утилита для фильтрации текста в файлах
- [**sed**](materials/linux/sed.md): Утилита для фильтрации и преобразования текста
- [__uniq__](#uniq): Исключение повторяющихся строк
- [__cat__](#cat): Объединение файлов, отображение содержимого файла
- [__echo__](#echo): Отображение строки текста
- [__fmt__](#fmt): Простое оптимальное форматирование текста
- [__wc__](#wc): Подсчет количества символов новой строки, слов и байт

### 📁 Файлы

- [__cd__](#cd): Изменение рабочей директории
- [**chmod**](materials/linux/chmod.md): Утилита для управления правами доступа
- **chown**: Утилита для управления владением
- [__cp__](#cp): Копирование файлов и каталогов
- [__ls__](#ls): Вывод содержимого директории
- [__mv__](#mv): Перемещение или переименование файлов
- [__mkdir__](#mkdir): Создание директории
- [__rm__](#rm): Удаление файлов или директорий
- [__touch__](#touch): Создание файлов

### 🚀 Процессы

- **top**: Утилита для вывода запущенных процессов и их потреблении ЦП
- [__kill__](#kill): Уничтожение активного процесса
- [__killall__](#killall): Уничтожение всех процессов, соответствующих заданным параметрам
- [__journalctl__](#journalctl): Журнал сообщений
- [**systemctl**](materials/linux/systemctl.md): Утилита для управления службами

### 🚀 Производительность

- [__nmon__](#nmon): Мониторинг производительности процессора, памяти, сети, дисков, файловой системы, ресурсов, микро раздела питания
- [__sar__](#sar): Генератор отчета с различной информацией об использовании и активности ресурсов в ОС

### 🧲 Сеть

- [__iptables__](materials/linux/iptables.md): Утилита для настройки и управления брандмауэром Linux
- [__scp__](#scp): Безопасное копирование файлов и каталогов между двумя системами
- [__ufw__](#ufw): Управление брандмауэром
- [**sshprank**](materials/linux/sshprank.md): Утилита для тестирование на проникновение в систему с использованием SSH

Конфигурация сети, средства устранения неполадок и отладки

- [**ifconfig**](materials/linux/ifconfig.md): Утилита для управления сетевыми интерфейсами. Аналог ip
- **ping**: Утилита для проверки соединения между узлами в сети с помощью ICMP-запросов
- [**traceroute**](materials/linux/traceroute.md): Утилита для отслеживания пути пакетов по сети от хоста до целевого узла
- **mtr**: Утилита для диагностики сети, которая сочетает в себе функции ping и traceroute
- **route**: Утилита для отображения или управления таблицей IP-маршрутизации системы Linux. В основном он используется для настройки статических маршрутов к определенным хостам или сетям через интерфейс
- **nmcli**: Утилита для сообщения о состоянии сети, управления сетевыми подключениями и управления NetworkManager
- [**ethtool**](materials/linux/ethtool.md): Утилита для низкоуровневой настройки сетевых карт

Сетевое сканирование и анализ производительности

- [**netstat**](materials/linux/netstat.md): Утилита для анализа сетевых подключений, таблицы маршрутизации, статистики сетевого интерфейса. Аналог ss
- [**ss**](materials/linux/ss.md): Утилита для исследования сокетов
- [**nc**](materials/linux/nc.md): Утилита для работы с сокетами TCP, UDP или UNIX-доменами
- [__nmap__](materials/linux/nmap.md): Сканер портов, запущенных служб, уязвимостей
- [**host**]: Утилита для поиска DNS, перевода имен хостов в IP-адреса и наоборот
- [**dig**](materials/linux/dig.md): Утилита для работы с доменами, IP-адресами, DNS-записями
- [**nslookup**](materials/linux/nslookup.md): Инструмент для запроса серверов системы доменных имен (DNS) для получения информации о хосте или разрешения IP-адреса

Анализ сетевых пакетов

- [**tcpdump**](materials/linux/tcpdump.md): Утилита для захвата трафика, мониторинга сетевой активности.
- **wireshark**: Утилита для захвата и анализа пакетов в сети с коммутацией пакетов в режиме реального времени с GUI.
- **bmon**: Утилита для сетевого мониторинга и отладки на основе командной строки для Unix-подобных систем.

### 🕵️‍♂️ Траблшутинг

- [**hping3**](materials/linux/hping3.md): Утилита для стресс-тестирования, тестирования брандмауэра, сканирования и генерации пакетов
- [**memdump**](#memdump): Инструмент для криминалистического анализа системной памяти. Извлекает ценную информацию из оперативной памяти компьютера во время инцидента или расследования
- [**masscan**](#masscan): Инструмент для сканирования портов. Способен сканировать весь Интернет за несколько минут
- [**arp**](#arp): Инструмент для отображения ARP таблицы
- **curl**: Утилита для передачи файлов из оболочки по таким протоколам, как HTTP, HTTPS, FTP, SFTP, FTPS или SCP
- [**dd**](materials/linux/dd.md): Инструмент для дублирования данных и криминалистической визуализации, который используется для обнаружения доказательств и сохранения цифровых данных для восстановления временных рамок событий. Используется для создания точной копии диска или файла, получения удаленных файлов из образа диска

### 📅 Бекап

- [**rsync**](materials/linux/rsync.md): Быстрое и безопасное создавание резервных копий файлов и папок в удаленном месте
- **Deja Dup**: Графический инструмент резервного копирования, который упрощает процесс резервного копирования, позволяя быстро и легко создавать резервные копии данных. Он предоставляет удобный интерфейс для создания резервных копий данных на локальном или удаленном носителе
- **Duplicity**: Графический инструмент резервного копирования, который обеспечивает комплексную защиту данных и безопасное резервное копирование

# 🗳️ Базы данных

- [Введение в базы данных](materials/db/db.md)
- [Автоматизация работы с БД](materials/db/automatization.md)
- [Безопасность БД](materials/db/security.md)
- [**PostgreSQL**](materials/db/postgresql.md): СУБД

# 🅰️ Языки программирования

- [**Python**](/materials/langs/python.md): Высокоуровневый язык программирования общего назначения
- [**SQL**](/materials/langs/sql.md): Язык программирования, используемый в большинстве реляционных БД для запросов, обработки и определения  данных, а также контроля доступа
- [**PHP**](/materials/langs/php.md): Язык программирования, который используется для web-разработки
- [**JS**](/materials/langs/js.md): Динамический язык программирования, который применяется к HTML документу

# 🌍 WEB

- [История web](materials/web/history.md)
- [**URL**](materials/web/url.md): Адрес WEB ресурса
- [Основа WEB - HTTP](materials/networks/http.md)
- [**WEB-сервер**](materials/web/web_server.md): web-server & web application server
- [**COOKIE**](materials/web/cookie.md): Файлы, отправляемые веб-сервером и хранимые у клиента
- [**SSL/TLS**](materials/networks/ssl_tls.md): Протокол шифрования трафика
- [**CDN**](materials/web/cdn.md): Инфраструктура для быстрой доставки контента
- [**Службы**](materials/web/services.md): Веб-службы
- [**API**](materials/web/api.md): Интерфейс для взаимодействия между программными компонентами
- [Очереди задач](materials/web/queue.md)
- [Полнотекстовый поиск](materials/web/full_text_search.md)
- [Хранилище данных](materials/web/data_warehouse.md)
- [Облачное хранилище](materials/web/cloud_storage.md)
- [Кэширование](materials/web/caching.md)
- [robots.txt, htaccess, sitemap и др.](materials/web/famous_files.md)
- [**HTML**](materials/web/html.md): Язык разметки документов
- [**XML**](materials/web/xml.md): Расширяемый язык разметки
- [**AJAX**](materials/web/ajax.md): Технология взаимодействия с сервером без перезагрузки
- [**JSON**](materials/web/json.md): Структурированный формат данных на основе синтаксиса JS
- [**CSS**](materials/web/CSS.md): Код стилизации веб-страниц
- [**LAMP stack**](materials/web/lamp_stack.md): Стек технологий для создания веб-приложений
- [**WAF**](materials/web/waf.md): Средства фильтрации трафика прикладного уровня

# 💻 DevOps

- [Что такое DevOps?](materials/devops/whatis_devops.md)
- [**Docker**](materials/devops/docker.md): Платформа для разработки, запуска и доставки контейнерных приложений
- [**LXC**](materials/devops/lxc.md): Контейнерная система виртуализации
- [**CI/CD**](materials/devops/ci_cd.md): Автоматизация этапов разработки
- [**Git**](materials/devops/git.md): Система контроля версий
- [**Контейнеризация**](materials/devops/containerization.md): Процесс развертывания ПО
- [**Kubernetes**](materials/devops/k8s.md): Платформа для управления кластерами контейнерных приложений
- [**Мониторинг**](materials/devops/monitoring.md): Процесс постоянного отслеживания и анализа состояния компонентов инфраструктуры
- [Безопасность контейнеров и кластеров](materials/devops/security.md)
- [**Ansible**](materials/devops/ansible.md): ПО, которое автоматизирует поставку и развертывание ПО

# 🛡️ Безопасность

- [**MFA**](materials/security/mfa.md): Многофакторная аутентификация
- [Кибербезопасность](materials/security/cybersecurity.md)
- [Информационная безопасность](materials/security/infosec.md)
- [Криптография](materials/security/crypto.md)
- [**OWASP TOP-10**](materials/security/owasp_top_ten.md): Рейтинг распространенных уязвимостей

### 🔥 Уязвимости веб-приложений

- [**XSS**](materials/security/xss.md): Атака, которая позволяет внедрять код через веб-сайт других пользователей
- [**CSRF**](materials/security/csrf.md): Атака, которая позволяет выполнять действия от лица другого пользователя на веб-сайте
- [**SQLi**](materials/security/sqli.md): Атака, которая позволяет выполнять произвольный SQL код в БД
- [**PHPi**](materials/security/phpi.md): Атака, которая позволяет внедрить php код в веб-приложение
- [**Path Traversal**](materials/security/path_traversal.md): Атака, которая позволяет получить доступ к файлам на сервере с помощью запроса
- [**RCE**](materials/security/rce.md): Атака, которая позволяет выполнить произвольный код на удаленном сервере

# 🪟 Windows

- [Windows](materials/windows/windows.md)

# 🪐 Сети

- [Начало](materials/networks/basic.md)
- [OSI](materials/networks/osi.md)
- [TCP/IP](materials/networks/tcp_ip.md)
- [Сетевое оборудование](materials/networks/nodes.md)
- [Техники сканирования сети](materials/networks/scan_techniques.md)
- [Защита коммутаторов](materials/networks/com_defense.md)
- [Защита сегментов сети. ACL](materials/networks/acl.md)
- [**VPN**](materials/networks/vpn.md): Частная виртуальная сеть
- [**websocket**](materials/networks/websocket.md)
- [**OAuth2**](materials/networks/oauth2.md)
- [**LDAP**](materials/networks/ldap.md)
- [**RTP**](materials/networks/rtp.md)
- [**NFC**](materials/networks/nfc.md)
- [**WPA**](materials/networks/wpa.md)
- [**SCTP**](materials/networks/sctp.md)
- [**NAT**](materials/networks/nat.md)
- [**NTP**](materials/networks/ntp.md)
- [**NETFLOW**](materials/networks/netflow.md)
- [**DMARC**](materials/networks/dmarc.md)

## Лабы

- [Вопросы](materials/labs/networks/answers.md)

## ⭐ L2. Канальный уровень

- [Коммутация](materials/networks/commutaion.md)
- [MAC-адрес](materials/networks/mac.md)
- [Топология сети](materials/networks/topology.md)
- [**STP**](materials/networks/stp.md): Протокол защиты от петель в сети
- [**VLAN**](materials/networks/vlan.md): Сегментация сети

### 📘 Протоколы

- [**ARP**](materials/networks/arp.md): Протокол, который соотносит MAC-адрес с IP-адресом

### ⚔️ Атаки

- [**MAC spoofing**](materials/networks/mac_address_spoofing.md): Подмена MAC-адреса
- [**CAM table Overflow**](materials/networks/cam_table_overflow.md): Переполнение таблицы коммутации
- [**ARP spoofing**](materials/networks/arp_spoofing.md): Подмена ARP запроса
- [**VLAN Hopping**](materials/networks/vlan_hopping.md): Подмена VLAN
- [**STP attacks**](materials/networks/stp_attacks.md): Дестабилизация коммутаторов

## ⭐ L3. Сетевой уровень

- [**Маршрутизация**](materials/networks/routing.md): Передача пакетов данных от отправителя к получателю
- [**Inter VLAN Routing**](materials/networks/inter_vlan_routing.md): Обмен данными между узлами из разных VLAN

### 📘 Протоколы

- [**IP**](materials/networks/ip.md): Протокол, который позволяет передавать весь трафик
- [**ICMP**](materials/networks/icmp.md): Протокол, который используется для проверки доступности того или иного хоста
- [**IGMP**](materials/networks/igmp.md): Протокол, который проверяет маршруты
- [**DVMRP**](materials/networks/dvmrp.md): Протокол, который проверяет маршруты
- [**IPsec**](materials/networks/ipsec.md): Протокол, который используется в VPN
- [**RIP**](materials/networks/rip.md): Протокол, который используется для динамической маршрутизации
- [**OSPF**](materials/networks/ospf.md): Протокол, который используется для динамической маршрутизации
- [**BGP**](materials/networks/bgp.md): Протокол, который используется для динамической маршрутизации

## ⭐ L4. Транспортный уровень

- [Что такое логический порт?](materials/networks/what_is_port.md)

### 📘 Протоколы

- [**TCP**](materials/networks/tcp.md): Протокол для надежной передачи данных по сети
- [**UDP**](materials/networks/udp.md): Протокол для передачи данных по сети

## ⭐ L7. Прикладной уровень

### 📘 Протоколы

- [**HTTP**](materials/networks/http.md): Протокол для передачи гипертекста
- [**SMB**](materials/networks/smb.md): Протокол для удаленного доступа к файлам или принтерам
- [**TELNET**](materials/networks/telnet.md): Протокол для реализации текстового терминального интерфейса по сети
- [**SSH**](materials/networks/ssh.md): Протокол для безопасного доступа к удаленным узлам сети
- [**DHCP**](materials/networks/dhcp.md): Протокол для автоматического назначения IP-адресов узлам сети
- [**DNS**](materials/networks/dns.md): Протокол для обращения к узлам сети по именам
- [**SMTP**](materials/networks/smtp.md): Протокол для отправки электронных писем
- [**FTP**](materials/networks/ftp.md): Протокол для передачи файлов по сети
- [**POP3**](materials/networks/pop3.md): Протокол для отправки электронных писем
- [**RDP**](materials/networks/rdp.md): Протокол для подключения к удаленным узлам сети
- [**IMAP**](materials/networks/imap.md): Протокол для отправки электронных писем

# 🚨 Как ...

- [Как настроить статический IP-адрес на linux хосте?](/materials/howto/howto_set_up_a_static_IP_address_on_a_linux_host.md)


# ❓ Что такое ...

- [Что такое регулярные выражения?](/materials/whatis/whatis_regex.md)
- [Что такое система контроля версий (git)?](/materials/whatis/whatis_git.md)

- [Sources](materials/links.md)
- [Glossary](materials/glossary.md)
