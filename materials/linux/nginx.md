## Nginx

Веб-сервер с открытым исходным кодом, созданный работать под высокой нагрузкой. Чаще всего используется для отдачи статического контента

### Установка

```bash
$ sudo apt install nginx
```

### Конфигурация

Файлы конфигурации nginx находятся в папке /etc/nginx. Рассмотрим их:

- `/etc/nginx/nginx.conf` – главный файл конфигурации nginx. В отличии от Apache вся конфигурация делается тут
- `/etc/nginx/sites-available` – каталог с конфигурациями виртуальных хостов, т.е. каждый файл, находящийся в этом каталоге, содержит информацию о конкретном сайте – его имени, IP адресе, рабочей директории и т.д.
- **`/etc/nginx/sites-enabled`** – в этом каталоге содержаться конфигурации сайтов, обслуживаемых nginx, т.е. активных, как правило, это символические ссылки sites-available конфигураций, что очень удобно для оперативного включения и отключения сайтов

Вначале необходимо создать корневой каталог. Назовем веб-проект example1 и положим туда html файл, содержащий в себе заголовок [example.com](http://example.com)

```bash
$ sudo mkdir -v /usr/share/nginx/example1.com
$ echo "<h1>Hello</h1>" | sudo tee /usr/share/nginx/example1.com/index.html
```

Удалим виртуальный хост по умолчанию, так как далее заменим его своим:

```bash
$ sudo rm /etc/nginx/sites-enabled/default
```

Теперь создаем файл виртуального хоста для домена [example1.com](http://example1.com) и внесем туда настройки

```bash
$ sudo nano /etc/nginx/sites-available/example1.com
```

Убедимся в отсутствии ошибок

```bash
$ sudo nginx -t
```

Активируем сайт, создав символическую ссылку на каталог sites-enabled:

```bash
$sudo ln -s /etc/nginx/sites-available/example1.com /etc/nginx/sites-enabled/example1.com
```


# Практика Nginx

Установка

```bash
$ sudo apt install nginx -y
```

### Проверка работы

Перейдем по своему локальному ip адресу (смотрим с помощью команды `ip a`). Если появляетс такая картинка, значит все работает верно

```bash
#создадим рабочий каталог
$ sudo mkdir /usr/share/nginx/example1.com

#создаем сам текст странички
$ echo "<h1'>Example1.com</h1>" | sudo tee /usr/share/nginx/example1.com/index.html

#удаляем дефолтную страничку
$ sudo rm /etc/nginx/sites-enabled/default

#проверка работы (должен написать ok)
$ sudo nginx -t

#открываем файл и вставляем код для работы сервера
$ sudo nano /etc/nginx/sites-available/example1.com
```

```php
server {
    #listen указывает на IP-адрес и порт, на котором программа будет ожидать соединения от этого сайта.
    #Чтобы выбрать любой IP-адрес, можно указать звездочку, а порт указывать обязательно.
    #параметр default server виртуальный хост будет использоваться по умолчанию
    listen *:80 default_server;
    #server name-Доменные имена, на которые будет отзываться этот хост
    server_name example.com www.example.com;
    #root-путь к файлам сайта, которые будут открываться при запросе к этому виртуальному хосту.
    #y Nginx должен быть доступ на чтение ко всем папкам по этому пути;
    root /usr/share/nginx/example1.com;
    #index-файлы, которые будут открываться, если адрес файла не указан в URL;
    index index.php index.html index.htm;
    #location-это набор правил обработки путей в url
    location / {
        try_files $uri $uri/ /index.php;
    }

    location ~ \\.php$ {
         fastcgi_pass unix:/run/php/php7.2-fpm.sock;
         include snippets/fastcgi-php.conf;
    }
}
```

```bash
$ sudo ln -s /etc/nginx/sites-available/example1.com /etc/nginx/sites-enabled/example1.com
$ service nginx restart
```

Снова переходим по этому адресу и видим нашу страничку
