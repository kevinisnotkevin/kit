# SMB

[lab](materials/labs/linux_labs/lab_15.md)

Samba - это программа, которая позволяет компьютерам UNIX/Linux имитировать машины Windows. При помощи Samba компьютеры могут обмениваться файлами или управлять заданиями на печать как файл-серверы или серверы печати под Windows. Подобно операционной системе UNIX/Linux, на которой она работает, Samba представляет собой разработанное множеством программистов ПО с открытым исходным кодом. Протокол SMB используется Microsoft Windows 2000, NT и 95/98 для организации доступа к дискам и принтерам. Используя набор утилит Samba от Andrew Tridgell, UNIX-машины (включая Linux) могут организовывать доступ к дискам и принтерам для Windows-машин.


В общей структуре корпоративных сетей Microsoft Windows и Linux зачастую существуют как разобщенные миры. Попытки обеспечить «прозрачный» доступ сетевых клиентов Windows к ресурсам UNIX зачастую порождают множество проблем. Одним из способов, позволяющих их решить, является использование в UNIX/Linux протокола Server Message Block (SMB — Samba), который иногда также называется протоколом Session Message Block (SMB), протоколом NetBIOS или протоколом LanManager. Этот протокол не является разработкой Microsoft, однако он хорошо адаптирован для использования в сетях на основе Windows. Хотя эта статья и посвящена Linux, тем не менее Samba может работать на большинстве UNIX-подобных операционных систем. И еще одно замечание: в данной статье мы рассмотрим пакет SMB с точки зрения малого предприятия, которому нужен локальный файл- или принт-сервер, либо домашнего пользователя, имеющего несколько компьютеров и желающего, чтобы машины совместно использовали ресурсы. Поэтому даже начинающий администратор UNIX/Linux может принять эту статью как руководство к действию.

Протокол SMB используется Microsoft Windows 2000, NT и 95/98 для организации доступа к дискам и принтерам. Используя набор утилит Samba от Andrew Tridgell ([Andrew.Tridgell@anu.edu.au](mailto:Andrew.Tridgell@anu.edu.au)), UNIX-машины (включая Linux) могут организовывать доступ к дискам и принтерам для Windows-машин. Утилиты smbfs, написанные Paal-Kr. Engstad ([pke@engstad.ingok.hitos.no](mailto:pke@engstad.ingok.hitos.no)) и Volker Lendecke ([lendecke@namu01.gwdg.de](mailto:lendecke@namu01.gwdg.de)), дают возможность машинам с UNIX монтировать SMB-ресурсы, доступные на машинах с Windows или Samba.

**C помощью Samba можно реализовать следующие наиболее распространенные:**

- Давать доступ к дискам Linux Windows-машинам.
- Получать доступ к дискам Windows для машин под Linux.
- Давать доступ к принтерам Linux для Windows-машин.
- Получать доступ к принтерам Windows из Linux-систем.

_для начала необходимо установить sambu_

`sudo apt-get install samba`


**переходим в конфигурационный файл**

`sudo nano /etc/samba/smb.conf`

всё о этом конфигурационном файле тут [https://smb-conf.ru/aio-write-behind-s.html](https://smb-conf.ru/aio-write-behind-s.html)

**есть команда**

`sudo testparm`

_Который позволяет убедиться о конфигурации samba и что она работает нормально_

_Давайте перейдём к настройке samba для того чтобы расшарить папку_


- [shared] — имя общей папки, которое увидят пользователи, подключившись к серверу.
- comment — комментарий для удобства.
- path — путь на сервере, где будут храниться данные.
- public — для общего доступа. Установите в yes, если хотите, чтобы все могли работать с ресурсом.
- writable — разрешает запись на сетевой ресурс.
- read only — только для чтения. Установите no, если у пользователей должна быть возможность создавать папки и файлы.
- guest ok — разрешает доступ к папке гостевой учетной записи.
- create mask, directory mask, force create mode, force directory mode — при создании новой папки или файла назначаются полные права.

1. john - имя шары
2. browseablr - чтобы была видна
3. path - директория
4. quest ok - важная опция, можно использовать любого пользователя
5. read only - можно добавлять и удалять анонимным пользователям

**Далее мы добавляем сервис в автозагрузку и запускаем его**

```
sudo systemctl enable smbd
sudo systemctl start smbd

```

затем для доступа через Windows надо сделать следующие

_Затем создаём текстовый файл и 222.txt редактируем, идём в linux и затем уже в директорию tmp_


# Практика Samba

```bash
#на всякий случай делаем бекап файла конфигураций Samba
$ cp /etc/samba/smb.conf /etc/samba/smb.conf.bak 

#cоздаем директорию, которая будет иметь общий доступ
$ mkdir -p /samba/public

#переходим в созданную директорию и устанавливаем права доступа на папку public
$ cd samba
$ chmod -R 0755 public

#добавляем новую директорию и создаем новых пользователя и группу
$ mkdir /samba/private
$ sudo groupadd smbgrp
$ sudo adduser user1

#меняем права доступа для директории private и добавляем ранее созданную группу
$ chgrp smbgrp /samba/private

#добавляем пароль на диркеторию для user1
$ sudo smbpasswd -a user1 

#переходим к редактированию конфигурационного файла Samba
$ nano /etc/samba/smb.conf
```

```
[global]
workgroup = WORKGROUP
security = user
map to guest = bad user
wins support = no
dns proxy = no

[public]
path = /samba/public
guest ok = yes
force user = nobody
browsable = yes
writable = yes

[private]
path = /samba/private
valid users = user1
guest ok = no
browsable = yes
writable = yes
```

Проверка настроек с помощью команды `testparm -s`, чтобы применить настройки `service smbd restart`

### Безопасность в Samba

> Iptables

```bash
#проверка установки iptables
$ iptables -L

#прописываем правило фаервола для ***разрешения*** (ACCEPT)
	- входящий пакетов 
	- по протоколу tcp
	- на порт 445
	- для адресов 10.0.0.0/24 (у вас ip может отличаться, можно посмотреть с помощью **ip a**)
$ iptables -A INPUT -p tcp -m tcp --dport 445 -s 10.0.0.0/24 -j ACCEPT

#аналогично для остальных портов (это те порты по которым работает Samba)
$ iptables -A INPUT -p tcp -m tcp --dport 139 -s 10.0.0.0/24 -j ACCEPT
$ iptables -A INPUT -p udp -m udp --dport 137 -s 10.0.0.0/24 -j ACCEPT
$ iptables -A INPUT -p udp -m udp --dport 138 -s 10.0.0.0/24 -j ACCEPT
```

### Установка утилиты для автоматической работы iptables

```bash
$ apt-get install iptables-persistent
```

Теперь в файле rules.v4 прописаны правила, которые мы указывали ранее

```bash
$ service netfilter-persistent save
```

### Проверка работы

Теперь попробуем создать папку в директории public. Все работает, без необходимости перезапускать сервер. Все настройки применяются автоматически
