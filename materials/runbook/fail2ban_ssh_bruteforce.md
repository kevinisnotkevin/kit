# Fail2Ban-SSH и Brute-force attack

### **Задача:**

1. Настроить SSH-сервер и SSH-клиент
2. Запретить доступ от root
3. Вывести журнал подключений пользователей SSH
4. Установить и настроить Fail2Ban для SSH
5. Провести несколько разных атак брутфорса


# 1. Обновление и загрузка Fail2Ban

## 1.1 **Для начала давайте обновим ОС:**

```bash
sudo apt-get update
```

## 1.2 **Далее давайте установим Fail2Ban:**

```bash
sudo apt-get install fail2ban
```

## 1.3 Запуск сервиса

Так как Fail2Ban запускается после перезагрузке или старте ОС сделаем следующее:

```bash
service fail2ban start
```

## 1.4 Изменение файла конфигурации

Теперь посмотрим файлы конфигурации

### 1.4.1 **Ознакомимся со всеми файлами в директории Fail2Ban:**

```bash
ls -al /etc/fail2ban
```
- самый важный файл - это **jail.conf** (данный файл содержит фильтры по умолчанию для определенных служб)

### 1.4.2 **Теперь давайте перейдём в нужную директорию и откроем файл конфигурации:**

```bash
cd /etc/fail2ban
nano jail.conf
```

И первое, что мы видим - это то, что нам рекомендуют создать дополнительный файл с `.local` расширением так как при обновлении `Fail2Ban` файл `jail.conf` может тоже обновиться и настройки будут возвращены по умолчанию. (если нужно настраивать фильтры для многих сервисов, то можно использовать `jail.conf` (но следить за обновлениями) или скопировать его с разрешением `.local`)

### 1.4.3 Давайте приступим к изменению файла

```bash
sudo nano jail.local
```

**Нам надо создать фильтр для ssh:**

```bash
[sshd]           # указывает, что это сервис ssh
enable = true    # *указывает на то, что данный фильтр включен (истина или ложь)* 
port = ssh       # указывем порт и log файл

```

**Для того чтобы нам для fail2ban дать информацию куда смотреть, делаем следующее:**

```jsx
cat /var/log/auth.log
```

**данный файл показывает все попытки аутентификации по sshd, т.е. нам надо указать в фильтре следующее:**

```bash
filter = sshd
logpath = /var/log/auth.log    # указываем где брать информацию
maxretry = 3                   # указываем какое количество попыток ввода пароля сделает пользователь прежде чем его заблокирует fail2ban
bantime = 3600                 # указывает на какое количество времени заблокируется ip-адрес пользователя после неправильного ввода пароля 3 раза (вводится секундами т.е. 3600 сек. это 60 мин.) Также можно установить и больше, всё завист от того как часто атакуют сервер.
ignoreip = 127.0.0.1           # белый список ip-адресов, которые не будут попадать под фильтр

```

**В итоге мы получаем текст файла следующий:**

```bash
[sshd]
enable = true
port = ssh
filter = sshd
logpath = /var/log/auth.log
maxretry = 3
bantime = 3600
ignoreip = 127.0.0.1

```

После сохраняем и выходим.

### 1.4.4 Перезапуск сервиса

Для того, чтобы применились новые настройки в конфигурационном файле, перезапустим сервис.

**Вводим:**

```bash
service fail2ban restart
```

**Проверим, что fail2ban работает:**

```bash
service fail2ban status
```

## 1.5 Теперь давайте попробуем подключиться по ssh и ввести три раза неправильно пароль

После того как попробуем, можно убедиться ещё одним способом, с помощью `Hydra`

```bash
sudo hydra -l root -P /usr/share/hydra/dpl4hydra_local.csv ssh://ip-адрес -I -V
```

### **Изменение порта**

Иногда админы изменяют порт SSH со стандартного 22 на другой порт. Чтобы использовать другой порт, используем аргумент -s:

```bash
sudo hydra -s номер_порта -l имя_пользователя -P словарь_для_брута ip-адрес ssh
```

### **Брутфорс списка IP-адресов**

**Можно также брутить несколько IP-адресов из списка -M:**

```bash
sudo hydra -l имя_пользователя -P словарь_для_брута -M список_ip_адресов ssh
```

**Так будет выглядеть простая команда:**

```bash
sudo hydra -l имя_пользователя -P список_паролей IP ssh
```

**где:**

- R – восстановить ранее прерванную сессию Hydra;
- S – использовать SSL для подключения;
- s – указать порт;
- l – использовать логин;
- L – выбирать логины из файла со списком;
- p – использовать пароль;
- P – использовать пароль из файла со списком;
- M – взять список целей из файла;
- x – генератор паролей;
- u – по умолчанию hydra проверяет все пароли для первого логина, эта опция позволяет проверить один пароль для всех логинов;
- f – выйти, если правильный логин/пароль найден;
- o – сохранить результат в файл;
- t – количество потоков для программы;
- w – время между запросами в секундах;
- v – подробный вывод;
- V – выводить тестируемые логины и пароли.
- 4:4:1 - пароль размером четыре символа, состоящий только из цифр. А именно все пароли в диапазоне 0000-9999;
- 4:8:1 - пароль от четырех до восьми символов, только из цифр;
- 4:5:aA1. - пароль, размером от 4 до 5 символов, состоит из цифр, букв верхнего и нижнего регистра или точки.

**если hydra не установлена, то вводим:**

```bash
sudo apt-get install hydra
```

**Чтобы посмотреть журналы банов в fail2ban вводим:**

```bash
fail2ban-client status
```

**и получить более подробно статистику:**

```bash
fail2ban-client status sshd
```

Если необходимо разблокировать ip-адрес попавшей по ошибке, то делаем следующее:

```bash
fail2ban-client unban [ip-адрес]
```

также можно запустить гидру и убедиться, что количество неудачных попыток ввода пароля увеличится.

Если в конфигурационном файле поменять на false, то ssh начнёт работать корректно и будет давать возможность подключаться.

```bash
[sshd]
enable = false
port = ssh
filter = sshd
logpath = /var/log/auth.log
maxretry = 3
bantime = 3600
ignoreip = 127.0.0.1
```

**Соответственно, если сбросить блокировку fail2ban, необходимо сделать следующее:**

```bash
service fail2ban restart
```

**и проверим, что никто в данный момент не блокируется:**

```bash
fail2ban-client status sshd
```

далее запускаем hydra ещё раз:

```bash
sudo hydra -l root -P /usr/share/hydra/dpl4hydra_local.csv ssh://ip-адресс -I -V
```

и проверим ещё раз список блокированных ip-адресов

```bash
fail2ban-client status sshd
```

**увидим что fail2ban отработал корректно и заблокировал ip-адрес:**

**Проверим, что будет, если отключить fail2ban и запустить hydra:**

```bash
service fail2ban stop
```
