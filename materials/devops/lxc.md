# Linux Containers



- Контейнеры позволяют быстро развернуть изолированную среду, соответствующую потребностям тестирования. Например, может потребоваться протестировать веб-приложение, требующее определенной версии базы данных или веб-сервера. Вместо того, чтобы настраивать эти компоненты на локальной машине, что может занять много времени и привести к ошибкам, можно создать контейнер, содержащий именно ту конфигурацию, которая нужна.
- __Linux Containers (LXC)__ — технология виртуализации, которая позволяет нескольким изолированным системам Linux работать на одном хосте. Она использует функции изоляции ресурсов, такие как _cgroups_ и _namespaces_, для обеспечения легкого решения виртуализации. Объединив LXC и Docker, пользователи могут получить полноценную возможность контейнеризации в системах Linux.
- __LXC__ — облегченная технология виртуализации, которая использует функции изоляции ресурсов ядра Linux для обеспечения изолированной среды для приложений. В LXC образы собираются вручную путем создания корневой файловой системы и установки необходимых пакетов и конфигураций. Эти контейнеры привязаны к хост-системе, их может быть сложно переносить, и для их настройки и управления могут потребоваться дополнительные технические знания. LXC также предоставляет некоторые функции безопасности, но может быть не таким надежным, как Docker.
- LXC требует, чтобы ядро ​​Linux поддерживало необходимые функции для контейнеризации.
- LXC могут использоваться для тестирования эксплойтов или вредоносных программ в контролируемой среде, где создан контейнер, имитирующий уязвимую систему или сеть, а затем используется этот контейнер для безопасного тестирования эксплойтов, не рискуя повредить локальные машины или сети. Важно настроить безопасность контейнера LXC, чтобы предотвратить несанкционированный доступ или вредоносные действия внутри контейнера.
- Доступ к контейнерам LXC можно получить с помощью различных методов, таких как SSH или консоль. Рекомендуется ограничить доступ к контейнеру, отключив ненужные службы, используя безопасные протоколы и внедрив механизмы строгой аутентификации. Например, можно отключить доступ по SSH к контейнеру, удалив пакет openssh-server или настроив SSH только на разрешение доступа с доверенных IP-адресов. Эти контейнеры также используют то же ядро, что и хост-система, а это означает, что они могут получить доступ ко всем ресурсам, доступным в системе. Можно использовать ограничения или квоты ресурсов, чтобы предотвратить чрезмерное потребление контейнерами ресурсов. Например, можно использовать cgroups, чтобы ограничить объем процессора, памяти или дискового пространства, которое может использовать контейнер.

```bash
sudo apt-get install lxc lxc-utils -y
```

- Установка LXC.

```bash
sudo lxc-create -n linuxcontainer -t ubuntu
```

- Создание нового контейнера LXC командой __lxc-create__, за которой следует имя контейнера __linuxcontainer__ и используемый шаблон __ubuntu__.



## Managing LXC Containers

- __lxc-ls__: Список всех существующих контейнеров;
- __lxc-stop -n <container>__: Остановить работающий контейнер;
- __lxc-start -n <container>__: Запустить остановленный контейнер;
- __lxc-restart -n <container>__: Перезапустить работающий контейнер;
- __lxc-config -n <container_name> -s storage__: Управление хранилищем контейнера;
- __lxc-config -n <container_name> -s network__: Управление настройками сети контейнера;
- __lxc-config -n <container_name> -s security__: Управление настройками безопасности контейнера;
- __lxc-attach -n <container>__: Подключиться к контейнеру;
- __lxc-attach -n <container> -f /path/to/share__: Подключиться к контейнеру и предоставить общий доступ к определенному каталогу или файлу.