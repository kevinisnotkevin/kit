# DoS на nginx с Fail2Ban

## **Цель работы:**

1. Развернуть сервер nginx
2. Защитить сервер nginx
3. Провести DOS-атаку на сервер nginx

## **Задача:**

1. Организовать атаку DoS;
2. Защитить сервер от DoS/DDoS атак через встроенные возможности nginx;
3. Защитить сервер от DoS/DDoS атак с помощью fail2ban при помощи iptables в автоматическом режиме;
4. Защитить сервер от DoS/DDoS атак с помощью fail2ban при помощи ipset в автоматическом режиме;

# **1. Разворачивание сервера nginx на базе дистрибутивов Debian.**

## 1.1 **Установите пакеты, необходимые для подключения apt-репозитория:**

```bash
sudo apt install curl gnupg2 ca-certificates lsb-release debian-archive-keyring
```

**Теперь нужно импортировать официальный ключ, используемый apt для проверки подлинности пакетов. Скачайте ключ:**

```bash
curl https://nginx.org/keys/nginx_signing.key | gpg --dearmor | sudo tee /usr/share/keyrings/nginx-archive-keyring.gpg >/dev/null
```

**Проверьте, верный ли ключ был загружен:**

```bash
gpg --dry-run --quiet --import --import-options import-show /usr/share/keyrings/nginx-archive-keyring.gpg
```

Вывод команды должен содержать полный отпечаток ключа

**573BFD6B3D8FBC641079A6ABABF5BD827BD9BF62:**

```
pub   rsa2048 2011-08-19 [SC] [expires: 2024-06-14]
      573BFD6B3D8FBC641079A6ABABF5BD827BD9BF62
uid                      nginx signing key <signing-key@nginx.com>

```


**Для подключения apt-репозитория для стабильной версии nginx, выполните следующую команду:**

```bash
echo "deb signed-by=/usr/share/keyrings/nginx-archive-keyring.gpg \\
http://nginx.org/packages/debian `lsb_release -cs` nginx" \\
    | sudo tee /etc/apt/sources.list.d/nginx.list

```

**Если предпочтительно использовать пакеты для основной версии nginx, выполните следующую команду вместо предыдущей:**

```
echo "deb signed-by=/usr/share/keyrings/nginx-archive-keyring.gpg \\
http://nginx.org/packages/mainline/debian `lsb_release -cs` nginx" \\
    | sudo tee /etc/apt/sources.list.d/nginx.list

```

**Для использования пакетов из нашего репозитория вместо распространяемых в дистрибутиве, настройте закрепление:**

```
echo -e "Package: *\\nPin: origin nginx.org\\nPin: release o=nginx\\nPin-Priority: 900\\n" \\
    | sudo tee /etc/apt/preferences.d/99nginx

```

**Чтобы установить nginx, выполните следующие команды:**

```bash
sudo apt update
```

```bash
sudo apt install nginx
```

_Если всё сделали правильно, то появится стартовая страница._

**если установлен nginx и apache2, то есть вероятность, что при запуске nginx будет появляться стартовая страничка apache2, в этом случае можно сделать следующее:**

```bash
cd /var/www/html
```

создать директорию, куда затем переместить или скопировать файл index.html

```bash
cp index.html folder1/
```

После этих манипуляций при запуске nginx будет появляться стартовая страничка nginx.

# **2. Далее переходим к настройке nginx от DDoS атак на наш сервер.**

**Устанавливаем fail2ban и ipset:**

```
apt install fail2ban

apt install ipset - дополнение, чтобы fail2ban мог эффективнее блокировать большое количество запросов

```

далее переходим:

nano /etc/nginx/sites-enable/default - тут находится стартовая страничка nginx

**выставляем следующие параметры в location:**

```bash
limit_req zone=ltwo burst=5 nodelay;
```

- limit_req - директива для zone=ltwo
- burst=5 - всплеск разрешён максимально 5
- и стоит опция nodelay, чтобы nginx не задерживал запросы

**далее переходим в конфигурационный файл nginx.conf:**

`nano /etc/nginx/nginx.conf` - **в данном файле нам надо внести следующие изменения:**

```
limit_conn_zone $binary_remote_addr zone=lone:10m;
limit_req_zone $binary_remote_addr zone=ltwo:10m rate=3r/s;
limit_req_zone $binary_remote_addr zone=highspeed:10m rate=10r/s

```

- ltwo - зона ограничения частоты запросов
- 10m - размер зоны в мегабайтах
- 3r/s - количество запросов в секунду

после чего вбиваем ip-адрес сервера в браузере, переходим в режим разработчика и далее делаем многочисленные запросы: в Chrome это - ctrl+r

после зажатия ctrl+r - выйдет ошибка 503

**данные ошибки будут отображаться в log файле:**

```bash
cat /var/log/nginx/error.log
```
данные log файл даст нам нужную информация для настройки фильтрации fail2ban.

# 3 Настройке fail2ban от DDoS атак на наш сервер

**Давайте найдём параметр бан в jail.conf или во вновь созданном файле jail.local внесём следующие параметры:**

```
[nginx-limit-req]
port = http, https
enabled = true
filter = nginx-limit-req
action = iptables-multiport[name=ReqLimit, port="http,https", protocol=tcp]
logpath = /var/log/nginx/*error.log
findtime = 600
bantime = 3600
maxretry = 4

```

после чего нам необходимо изменить настройку, что iptables не отсылал reject а отсылал drop, так как для борьбы с ботами - это более эффективно.

**Далее переходим в файл:**

```bash
nano /etc/fail2ban/action.d/iptables-common.conf
```


**но как мы уже знаем, настройки конфигурационных файлов по умолчанию могут быть перезаписаны при обновлении, поэтому создаём новый файл:**

```bash
touch iptables-blocktype.local
```

**где нам нужно внести следующую настройку:**

```
[Init]
blocktype = DROP

```

**Затем делаем рестарт fail2ban:**

```bash
service fail2ban restart
```

**Теперь нам нужно посмотреть, что iptables отрабатывает и наберём:**`iptables -L -v`

**Убедимся. что настройки в fail2ban применились и введём:**

```bash
fail2ban-client status
```

_видим, что фильтры работают по nginx-limit-req, sshd_

также проверим, что сейчас нет заблокированных ip-адресов:

```bash
fail2ban-client status nginx-limit-req
```

_теперь переходим на нашу страничку и вводим ip-адрес сервера в браузере, переходим в режим разработчика и далее делаем многочисленные запросы: в Chrome это - ctrl+r_

и видим вот такое сообщение после многочисленных запросов: "Не удается получить доступ к сайту"

**переходим на сервер и смотрим что у нас показывает iptables и fail2ban:**

`iptables -L -v`

_Видим, что добавилось правило_

**Далее переходим и смотрим, что показывает fail2ban:**

_Тут как раз показывает ip-адрес компьютера, который делал многочисленные запросы._

**Разблокировать нужный ip-адрес возможно с помощью слудующей команды:**

`fail2ban-client unban [ip-адрес]`

**Теперь, если нам необходимо, что-бы наш фильтр работал не через iptables а ipsec, то нам нужно сделать следующее:**

_зайти в конфигурационный файл jail.local и в нём закомментировать параметр action._

**далее перейдём на нашу страничку и попробуем повторить dos-атаку:**


**после того как мы добились блокировки нашего ip-адреса посмотрим, что же нам скажет ipset:**

```bash
ipset -L
```


_и видим, что нас заблокировал сейчас не iptables а ipset_

**fail2ban нам тоже скажет, что ip-адресс заблокирован:**
