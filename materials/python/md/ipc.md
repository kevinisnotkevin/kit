# Сети и межпроцессорное взаимодействие
1. [asyncio](#asyncio): Асинхронный ввод-вывод;
2. [socket](#socket): Низкоуровневый сетевой интерфейс;
3. [ssl - Оболочка TLS/SSL для объектов сокетов](#3)
4. [select](#select): Ожидание завершения ввода-вывода;
5. [selectors - Высокоуровневое мультиплексирование ввода-вывода](#)
6. [signal - Установите обработчики асинхронных событий](#6)
7. [mmap - Поддержка файлов, отображаемых в памяти](#7)


### asyncio
> Модуль предоставляет фукнции и классы для написания параллельного кода с использованием синтаксиса async/await. 
#### Использование
- Низкоуровневый API для разработчиков библиотек.
- Высокоуровневый API для одновременного запуска сопрограмм, выполнения сетевого ввода-вывода, управления подпроцессами, распределения задач по очередям, синхронизации параллельного кода.
- Автоматизация задач, которые зависят от событий, таких как таймеры или изменения состояния.
- Асинхронные подключения к БД позволяют выполнять другие задачи во время ожидания данных.
- Обработка большого количества одновременных веб-запросов без создания большого числа потоков или процессов.
#### Пример 1
```python
async def hello_world():
    print("Hello")
    await asyncio.sleep(1)
    print("World")

# Создаем цикл событий и запускаем асинхронную функцию
asyncio.run(hello_world())
```
#### Пример 2
```python
import asyncio


async def print_nums():
	num = 1
	while True:
		print(num)
		num += 1
		await asyncio.sleep(1)


async def print_time():
	count = 0
	while True:
		if count % 3 == 0:
			print(f"{count} секунд прошло")
		count += 1
		await asyncio.sleep(1)


async def main():
	task1 = asyncio.create_task(print_nums())
	task2 = asyncio.create_task(print_time())
	await asyncio.gather(task1, task2)


if __name__ == '__main__':
	asyncio.run(main())
```
#### Пример 3
```python
def write_image(data):
	filename = f"file_{int(time() * 1000)}.jpeg"
	with open(filename, 'wb') as file:
		file.write(data)


async def fetch_content(url, session):
	async with session.get(url, allow_redirects=True) as response:
		data = await response.read()
		write_image(data)


async def main():
	url = 'https://loremflickr.com/320/240'
	tasks = list()
	
	async with aiohttp.ClientSession() as session:
		for i in range(10):
			tasks.append(asyncio.create_task(fetch_content(url, session)))
			await asyncio.gather(*tasks)

if __name__ == '__main__':
	t0 = time()
	asyncio.run(main())
	print(time() - t0)
```


## socket
> Модуль предоставляет константы, исключения, функции для работы с сокетами, что позволяет создавать сетевые приложения. Сокеты являются основным механизмом взаимодействия между компьютерами в сети.
### Использование
- Создание сервера, который прослушивает определенный порт и ожидает подключений от клиентов.
- Создание клиентского приложения, которое подключается к серверу по определенному адресу и порту.
- Передача данных между клиентом и сервером.
- Обмен данными без постоянного соединения.
#### Пример 1

```python
import socket

# Создание серверного сокета
server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server_socket.bind(('localhost', 8081))
server_socket.listen(5)  

# Создание клиентского сокета
def accept_connection():
	while True:
		client_socket, addr = server_socket.accept()
		send_message(client_socket)


def send_message(client_socket):
	while True:
		request = client_socket.recv(1024)
		print(request.decode())
		
		if not request:
			break
		else:
			response = client_socket.send(b'Hello\n')
		client_socket.close()



accept_connection()
```
#### Пример 2
```python
server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server_socket.bind(('localhost', 8080))
server_socket.listen(5)

while True:
    client_socket, addr = server_socket.accept()


client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
client_socket.connect(('localhost', 8080))


client_socket.send(b'Hello, server!')

# Пример получения данных на сервере от клиента
data = client_socket.recv(1024)


udp_socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
udp_socket.sendto(b'Hello, server!', ('localhost', 8080))
```


## <div id="3">3. ssl - Оболочка TLS/SSL для объектов сокетов</div>
> Модуль предоставляет константы, исключения, функции и классы для работы с SSL/TLS (Secure Sockets Layer) протоколами, которые обеспечивают шифрование данных в сети. Использует библиотеку OpenSSL.
### Использование
- Используется для создания защищенных соединений между клиентами и серверами, обеспечивая конфиденциальность и целостность данных, передаваемых по сети.
- Поддержка безопастного соединения HTTPS, обеспечивая защищенную передачу данных между клиентами и серверами.
### Примеры
Создание простого HTTP-сервера, который обеспечивает защищенное соединение с использованием SSL.
```python
from http.server import HTTPServer, SimpleHTTPRequestHandler

# Генерация самоподписанного сертификата (для примера)
httpd = HTTPServer(('localhost', 4443), SimpleHTTPRequestHandler)
httpd.socket = ssl.wrap_socket(httpd.socket, certfile='path/to/server.pem', server_side=True)

# Запуск сервера
httpd.serve_forever()
```


## select
> Модуль предоставляет функции для ожидания возможности чтения или записи в нескольких файловых обьектах (сокетах) одновременно без блокировки выполнения программы.
### Использование
- Используется в сетевом программировании, когда необходимо эффективно управлять множеством сокетов.
#### Пример 1
Ожидание соединения на серверном сокете и возможность одновременной обработки данных из нескольких клиентских соединений без блокировки выполнения.
```python
server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server_socket.bind(('localhost', 12345))
server_socket.listen(5)

inputs = [server_socket]
outputs = []

while inputs:
    readable, writable, exceptional = select.select(inputs, outputs, inputs)
    
    for s in readable:
        if s is server_socket:
            # Принимаем новое соединение
            connection, address = s.accept()
            inputs.append(connection)
        else:
            # Читаем данные из существующего соединения
            data = s.recv(1024)
            if data:
                # Обрабатываем данные
                print(f"Received data: {data.decode()}")
            else:
                # Соединение закрыто
                inputs.remove(s)
                s.close()
```
#### Пример 2
Мини веб-сервер
```python
import socket
from select import select


to_monitor = []


server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server_socket.bind(('localhost', 8081))
server_socket.listen(5)


def accept_connection(server_socket):
	client_socket, addr = server_socket.accept()
	print(addr)
	send_message(client_socket)
	to_monitor.append(client_socket)
  

def send_message(client_socket):
	request = client_socket.recv(1024)
	print(request.decode())
	if request:
		response = client_socket.send(b'Hello\n')
	else:
		client_socket.close()


def event_loop():
	while True:
		ready_to_read, _, _ = select(to_monitor, [], [])
		for sock in ready_to_read:
			if sock is server_socket:
				accept_connection(sock)
			else:
				send_message(sock)


to_monitor.append(server_socket)
event_loop()
```


## selectors
> Модуль предоставляет классы, которые обеспечивают многопоточный ввод-вывод, основанный на модуле `select`.
### Использование
- Обеспечение эффективного обработчика событий ввода-вывода без блокировки потоков выполнения. Полезно в асинхронном программировании, где один поток может обрабатывать несколько событий одновременно без блокировки.
#### Пример 1
Простая реализация эхо-сервера:
```python
sel = selectors.DefaultSelector()

def accept(sock, mask):
    conn, addr = sock.accept()  # Should be ready
    print('accepted', conn, 'from', addr)
    conn.setblocking(False)
    sel.register(conn, selectors.EVENT_READ, read)

def read(conn, mask):
    data = conn.recv(1000)  # Should be ready
    if data:
        print('echoing', repr(data), 'to', conn)
        conn.send(data)  # Hope it won't block
    else:
        print('closing', conn)
        sel.unregister(conn)
        conn.close()

sock = socket.socket()
sock.bind(('localhost', 1234))
sock.listen(100)
sock.setblocking(False)
sel.register(sock, selectors.EVENT_READ, accept)

while True:
    events = sel.select()
    for key, mask in events:
        callback = key.data
        callback(key.fileobj, mask)
```
#### Пример 2
Веб-сервер
```python
import socket
import selectors


selector = selectors.DefaultSelector()


def server():
	server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	server_socket.bind(('localhost', 8081))
	server_socket.listen(5)
	selector.register(fileobj=server_socket, events=selectors.EVENT_READ, data=accept_connection)


def accept_connection(server_socket):
	client_socket, addr = server_socket.accept()
	print(addr)
	selector.register(fileobj=client_socket, events=selectors.EVENT_READ, data=send_message)


def send_message(client_socket):
	request = client_socket.recv(1024)
	
	print(request.decode())
	if request:
		response = client_socket.send(b'Hello\n')
	else:
		selector.unregister(client_socket)
		client_socket.close()


def event_loop():
	while True:
		events = selector.select()
		for key, _ in events:
			callback = key.data
			callback(key.fileobj)


server()
event_loop()
```

## <div id="6">6. signal - Установите обработчики асинхронных событий</div>
> Модуль предоставляет классы, переменные, функции для обработки сигналов ОС. Сигналы - уведомления, отправляемые процессам ОС для сообщения о событиях или запроса их завершения.
### Использование
- Реализация корректного завершения программы или выполнения дополнительных действий при получении определенных сигналов.
### Примеры
Ожидание сигнала SIGINT, и если он получен (например, при нажатии на Ctrl+C), будет вызван `handler()`.
```python
def handler(signum, frame):
    print(f"Received signal {signum}")

# Присвоение пользовательского обработчика сигнала SIGINT (Ctrl+C)
signal.signal(signal.SIGINT, handler)

print("Waiting for SIGINT...")
time.sleep(10)
```


## <div id="7">7. mmap - Поддержка файлов, отображаемых в памяти</div>
> Модуль предоставляет константы и классы для работы с отображаемыми в памяти файлами. Позволяет создавать обьекты, представляющие собой отображение файла в виртуальную память.
### Использование
- Отображение файла в память позволяет читать и записывать данные в файл как в обычный буфер в памяти, что ускоряет операции ввода-вывода.
- Обмен данными между процессами, так как отображаемый в памяти файл является разделяемым ресурсом.
- Работа с частями большого файла.
### Примеры
Отображение файла в память:
```python
with open('example.txt', 'r+') as file:
    # Создаем объект mmap для отображения файла в память
    mmapped_file = mmap.mmap(file.fileno(), 0)

    # Теперь можно работать с объектом mmapped_file, как с обычным буфером в памяти

    # Например, чтение первых 10 байт
    data = mmapped_file[:10]

    # Или запись данных в файл
    mmapped_file[10:20] = b'abcdefghij'

    # Не забываем закрыть отображение файла в память
    mmapped_file.close()
```
