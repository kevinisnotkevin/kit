# Интернет-протоколы и поддержка
1. [webbrowser - Контроллер веб-браузера](#1)
2. [wsgiref - Утилиты WSGI и эталонная реализация](#2)
3. [urllib - Модули обработки URL-адресов](#3)
4. [http - HTTP-модули](#4)
5. [ftplib - Клиент FTP-протокола](#5)
6. [poplib - Клиент протокола POP3](#6)
7. [imaplib - Клиент протокола IMAP4](#7)
8. [smtplib - Клиент протокола SMTP](#8)
9. [uuid - Объекты UUID](#9)
10. [socketserver - Фреймворк для сетевых серверов](#10)
11. [xmlrpc - Серверные и клиентские модули XMLRPC](#11)
12. [ipaddress - Библиотека манипуляций IPv4/IPv6](#12)


## <div id="1">1. webbrowser - Контроллер веб-браузера</div>
> Модуль предоставляет функции для открытия веб-страниц, URL-адресов, файлов в веб-браузере.
> ### Использование
- Открытие URL через веб-браузеры.
### Примеры
Открытие новой вкладки в существующем веб-браузере:
```python
webbrowser.open_new_tab('https://www.google.com/')
```
Открытие нового экземляра веб-браузера:
```python
webbrowser.open_new('https://www.google.com/')
```
Открывает URL в веб-браузере:
```python
webbrowser.open('https://www.google.com/')
```


## <div id="2">2. wsgiref - Утилиты WSGI и эталонная реализация</div>
> Модуль предоставляет классы и функции для управления переменными среды WSGI (Web Server Gateway Interface) и заголовками ответов, реализации серверов WSGI. Предоставляет демонстрационных HTTP-сервер, обслуживающих приложения WSGI. 
### Использование
- Полезен для понимания основ работы WSGI. Для реальных проектов обычно используют более продвинутые веб-серверы и фреймворки.
### Примеры
В этом примере `simple_app` - WSGI-совместимая функция, которая принимает словарь с данными окружения сервера и функцию установки статуса и заголовков ответа. Приложение возвращает содержимое ответа в виде списка байт:
```python
def simple_app(environ, start_response):
    status = '200 OK'
    headers = [('Content-Type', 'text/plain; charset=utf-8')]
    start_response(status, headers)
    return [b'Hello world']


if __name__ == '__main__':
    with make_server('', 8000, simple_app) as httpd:
        print('Запущен на порту 8000...')
        httpd.serve_forever()
```


## <div id="3">3. urllib - Модули обработки URL-адресов</div>
> Модуль предоставляет функции, исключения и классы для доступа в Интернет, обработки интернет-протоколов, получения данных из URL-адресов. Внешний модуль `requests` является интерфейсом HTTP более высокого уровня.
### Использование
- Парсинг данных из Интернета.
- Обработка URL-адресов.
- Анализ robots.txt.
### Примеры
В этом примере получается страница и возвращается обьект bytes и отображаются первые 300 байтов:
```python
with urllib.request.urlopen('http://www.python.org/') as file:
    print(file.read(300))
```
На основе предыдущего примера выводим декодированные данные:
```python
f = urllib.request.urlopen('http://www.python.org/')
print(f.read(100).decode('utf-8'))
```
Добавление заголовков:
```python
req = urllib.request.Request('http://www.example.com/')
req.add_header('Referer', 'http://www.python.org/')
r = urllib.request.urlopen(req)
```
Разбор URL для получения схемы, хоста, пути, параметров:
```python
urllib.parse.urlparse('https://www.example.com/path/page?param=value')
```


## <div id="4">4. http - HTTP-модули</div>
> Модуль предоставляет классы и функции для работы с протоколом HTTP, реализации управления состоянием с помощью файлов cookie, сохранения файлов cookie. Содержит реализацию клиентской части протоколов HTTP и HTTPS (Обычно не используется напрямую - модуль `urllib.request` использует его для обработки URL-адресов, использующих HTTP и HTTPS). Внешний модуль `requests` рекомендуется использовать для клиентского интерфейса HTTP более высокого уровня. `http.server` не рекомендуется использовать для производства, так как реализует только базовые проверки безопасности.
### Использование
- Создавание HTTP-серверов и клиентов, отправка запросов и обработка ответов.
- Работа с HTTP, cookie, обработка заголовков, управление сессиями, отправка файлов.
### Примеры
Реализация HTTP-клиента и обработка HTTP-заголовков и ответов:
```python
conn = http.client.HTTPSConnection('www.python.org')
conn.request('GET', '/')
response = conn.getresponse()
print(response.status, response.reason)
data = response.read()
conn.close()
```
Создание HTTP-сервера:
```python
from http.server import BaseHTTPRequestHandler, HTTPServer


class SimpleHandler(BaseHTTPRequestHandler):
    def do_GET(self):
        self.send_response(200)
        self.send_header('Content-type', 'text/html')
        self.end_headers()
        self.wfile.write(b'Hello world')


server_address = ('', 8000)
httpd = HTTPServer(server_address, SimpleHandler)
httpd.serve_forever()
```


## <div id="5">5. ftplib - Клиент FTP-протокола</div>
> Модуль предоставляет классы и функции для реализации клиентской части протокола FTP. Используется модулем `urllib.request` для обработки URL-адресов, использующих FTP.
### Использование
- Зеркалирование других FTP-серверов.
### Примеры
В этом примере устанавливаем соединение с FTP-сервером, выполняем аутентификацию, переходим в нужную директорию на сервере и передаем файл:
```python
from ftplib import FTP

# Создаем объект FTP
ftp = FTP()

# Подключаемся к FTP-серверу
ftp.connect("ftp.example.com")

# Входим на FTP-сервер (необходима авторизация)
ftp.login("username", "password")

# Переходим в нужную директорию на FTP-сервере
ftp.cwd("/remote/directory")

# Открываем локальный файл для передачи
with open("local_file.txt", "rb") as file:
    # Передаем файл на FTP-сервер
    ftp.storbinary("STOR remote_file.txt", file)

# Закрываем соединение
ftp.quit()
```


## <div id="6">6. poplib - Клиент протокола POP3</div>
> Модуль предоставляет классы и функции для взаимодействия с почтовым сервером по протоколу POP3 (Post Office Protocol 3). Считается устаревшим, лучше использовать `imaplib.IMAP4`.
### Использование
- Получение email с сервера.
### Примеры
Получение списка писем и текста первого письма:
```python
# Установка соединения с сервером
pop_conn = poplib.POP3('pop.example.com')

# Аутентификация пользователя
pop_conn.user('your_username')
pop_conn.pass_('your_password')

# Получение списка писем
email_list = pop_conn.list()
print("Список писем:", email_list)

# Получение текста первого письма
message_num = 1
email_text = pop_conn.retr(message_num)
print("Текст письма №", message_num, ":", email_text)

# Закрытие соединения
pop_conn.quit()
```


## <div id="7">7. imaplib - Клиент протокола IMAP4</div>
> Модуль предоставляет классы и функции для работы с протоколом почтового доступа IMAP (Internet Message Access Protocol).
### Использование
- Используется для взаимодействия с почтовыми серверами, позволяя клиентам получать и управлять email на удаленном сервере.
### Примеры
Подключение к почтовому серверу, выбор ящика и получение списка сообщений:
```python
# Подключение к почтовому серверу
mail = imaplib.IMAP4('your_mail_server.com')

# Вход с указанием имени пользователя и пароля
mail.login('your_username', 'your_password')

# Выбор почтового ящика (например, "inbox")
mail.select('inbox')

# Поиск всех сообщений в текущем ящике
status, messages = mail.search(None, 'ALL')
message_ids = messages[0].split()

# Получение информации о первом сообщении
status, msg_data = mail.fetch(message_ids[0], '(RFC822)')
print(msg_data[0][1])

# Закрытие соединения
mail.close()
mail.logout()
```


## <div id="8">8. smtplib - Клиент протокола SMTP</div>
> Модуль предоставляет функции и классы для отправки email через протокол SMPT (Simple Mail Transfer Protocol). Необходимо наличие доступа к SMTP-серверу и учетной записи для аутентификации.
### Использование
- Используется для передачи email между серверами электронной почты.
- Отправка электронных писем.
### Примеры
Создание обьекта SMTP, установка соединения с сервером, выполнение аутентификации, формирование MIME-сообщения и отправка письма:
```python
from email.mime.text import MIMEText

# Задаем параметры
smtp_server = 'smtp.example.com'
smtp_port = 587
smtp_username = 'your_username'
smtp_password = 'your_password'

from_addr = 'your_email@example.com'
to_addr = 'recipient@example.com'
subject = 'Тема письма'
body = 'Текст письма'

# Формируем сообщение
msg = MIMEText(body)
msg['Subject'] = subject
msg['From'] = from_addr
msg['To'] = to_addr

# Устанавливаем соединение и отправляем письмо
with smtplib.SMTP(smtp_server, smtp_port) as server:
    server.starttls()  # Используем шифрование TLS
    server.login(smtp_username, smtp_password)
    server.sendmail(from_addr, to_addr, msg.as_string())
```


## <div id="9">9. uuid - Объекты UUID</div>
> Модуль предоставляет классы и функции для работы с UUID (Universally Unique Identifier) (уникальными идентификаторами). UUID - 128-битное число.
### Использование
- Идентификация чего-либо.
### Примеры
Генерация UUID на основе текущего времени и уникального идентификатора узла (обычно MAC-адреса):
```python
uuid.uuid1()
```
Генерация UUID на основе переданного пространства и имени с использованием хэш-функции MD5:
```python
uuid.uuid3(uuid.NAMESPACE_DNS, 'example.com')
```
Генерация случайного UUID:
```python
uuid.uuid4()
```
Генерация UUID на основе переданного пространства и имени с использованием хэш-функции SHA-1.
```python
uuid.uuid5(uuid.NAMESPACE_DNS, 'example.com')
```


## <div id="10">10. socketserver - Фреймворк для сетевых серверов</div>
> Модуль предоставляет классы для создания серверов, обрабатывающих сокеты. Модуль упрощает задачу написания сетевых серверов. Является высокоуровневым модулем. Поддерживает обработку нескольких соединений параллельно.
### Использование
- Создание базовых веб-серверов, обрабатывающих HTTP-запросы, для простых веб-приложений или тестирования.
- Создание сетевых приложений, например, мессенджеры, игры, чат-серверы и т.д.
### Примеры
Сервер слушает соединения на localhost:8888 и использует `MyTCPHandler` для обработки входящих запросов. Когда клиент устанавливает соединение, сервер принимает данные, выводит их и отправляет обратно сообщение:
```python
class MyTCPHandler(socketserver.BaseRequestHandler):
    def handle(self):
        # Обработка соединения
        data = self.request.recv(1024).strip()
        print(f"Received data: {data}")
        # Отправка ответа обратно клиенту
        self.request.sendall(b"Server received your data.")

if __name__ == "__main__":
    # Создание TCP-сервера, привязка к localhost и порту 8888
    with socketserver.TCPServer(("localhost", 8888), MyTCPHandler) as server:
        print("Server running on localhost:8888")
        # Запуск сервера
        server.serve_forever()
```


## <div id="11">11. xmlrpc - Серверные и клиентские модули XMLRPC</div>
> Модуль предоставляет функции и классы для реализации XML-RPC. Модуль не защищен от вредоносно созданных данных.

XML-RPC (XML Remote Procedure Call) - метод удаленного вызова процедур, который использует XML, передаваемый через HTTP, в качестве транспорта. С его помощью клиент может вызывать методы с параметрами на удаленном сервере и получать обратно структурированные данные. Иными словами, это простой протокол удаленного вызова процедур, который использует XML для кодирования вызовов и ответов.
### Использование
- Вызов удаленных методов и передача данных между программами на разных компьютерах.
### Примеры
Сервер:
```python
from xmlrpc.server import SimpleXMLRPCServer

def add(x, y):
    return x + y

server = SimpleXMLRPCServer(('localhost', 8000))
server.register_function(add, 'add')
server.serve_forever()
```
Клиент:
```python
from xmlrpc.client import ServerProxy

proxy = ServerProxy('http://localhost:8000')
result = proxy.add(3, 5)
print(result)  # Вывод: 8
```


## <div id="12">12. ipaddress - Библиотека манипуляций IPv4/IPv6</div>
> Модуль предоставляет функции и классы для работой с адресами и сетями IPv4 и IPv6. Модуль упрощает выполнение различных задач, связанных с IP-адресами, включая проверку того, находятся ли два хоста в одной подсети, перебор всех хостов в определенной подсети, проверку того, представляет ли строка действительный адрес.
### Использование
- Работа с сетевым программированием, конфигурацией сетевых устройств, выполнение различных проверок и манипуляции с IP-адресами.
### Примеры
Создание обьектов, представляющих конкретные IP-адреса:
```python
from ipaddress import IPv4Address, IPv6Address

ipv4 = IPv4Address('192.168.1.1')
ipv6 = IPv6Address('2001:db8::1')
```
Проверка вхождения адреса в сеть:
```python
from ipaddress import IPv4Network, IPv6Network

subnet4 = IPv4Network('192.168.1.0/24')
subnet6 = IPv6Network('2001:db8::/64')
```
