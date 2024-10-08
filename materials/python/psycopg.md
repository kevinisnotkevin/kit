# PostgreSQL в Python

## 1 Организация доступа к базе данных

По умолчанию PostgreSQL доступна только на петлевом интерфейсе, соответственно, получить до нее доступ из сети невозможно. Для того, чтобы мы могли получить доступ необходимо сделать несколько шагов.

```
listen_addresses = '*'
```
- Указать прослушиваемые адреса в файле конфигурации `/etc/postgresql/XX/main/postgresql.conf`, где XX - версия postgresql.

```
port = 5432 # 5432 - порт по умолчанию
```
- Укажем, что прослушиваем на всех сетевых интерфейсах. Также хорошей практикой является изменение порта по умолчанию. После внесения изменений необходимо сохранить файл и закрыть его.

```jsx
host all all 192.168.133.1/32 password
```
- Перейдем в файл `/etc/postgresql/XX/main/pg_hba.conf`, где XX - версия postgresql. В конец файла необходимо записать следующую строчку.
- Здесь мы указываем: `host` - тип подключения, `all` - база данных, `all` - пользователь, `192.168.133.1/32` - адрес, с которого выполняется подключение, `password` - метод аутентификации.

```
service postgresql restart
```
- Перезапустим сервис

```
service postgresql status
```
- Убедимся в корректности работы

```
su - postgres

psql -c "ALTER ROLE postgres PASSWORD 'Qq12345';"
```
- Изменим пароль для пользователя postgres

## 2 Модуль psycopg2

Psycopg - самый популярный адаптер базы данных PostgreSQL для языка программирования Python. Его основными особенностями являются полная реализация спецификации Python DB API 2.0 и потокобезопасность (несколько потоков могут использовать одно и то же соединение). Он был разработан для многопоточных приложений, которые создают и уничтожают множество курсоров и выполняют большое количество одновременных операций INSERT или UPDATE.

```
pip install psycopg2
```
- Аналогично другим пакетам для начала необходимо его установить.

```python
import psycopg2
from psycopg2 import Error
```
- Уже в коде будет необходимо импортировать этот пакет.

```python
connection = psycopg2.connect(
	user="username",
	password="password",
	host="host",
	port="port", 
	database="database"
)
```
- После этого мы передаем в переменную connect соединение.

```python
cursor = connection.cursor()
```
- Создаем курсор

```python
cursor.execute("SELECT * FROM <table_name>")
row = cursor.fetchone()
cursor.close()
connect.close()
```
- С помощью метода execute делаем запрос к БД. После этого получаем одну строку результата с помощью fetchone. После чего разрываем подключение к базе данных, закрывая и курсор, и соединение.

```python
import psycopg2
from psycopg2 import Error

try:
    connection = psycopg2.connect(user="username",
                                  password="password",
                                  host="host",
                                  port="port", 
                                  database="database")

    cursor = connection.cursor()
    cursor.execute("some_command")
    print ("Команда успешно выполнена")
except (Exception, Error) as error:
    print("Ошибка при работе с PostgreSQL", error)
finally:
		if connection:
        cursor.close()
        connection.close()
        print("Соединение с PostgreSQL закрыто")
```
- Также стоит обернуть это программу в структуру try-except-finally.

```python
psql -c "CREATE DATABASE mydatabase;"
psql mydatabase
CREATE TABLE users (
	id SERIAL PRIMARY KEY,
	name VARCHAR(255),
	surname VARCHAR(255)
);

INSERT INTO users (name, surname) VALUES ('Иван', 'Иванов');
INSERT INTO users (name, surname) VALUES ('Петр', 'Петров');
```
- Создадим тестовую базу данных.

### 2.1 Операция Select

```python
import logging
import psycopg2
from psycopg2 import Error

logging.basicConfig(
    filename='app.log', format='%(asctime)s - %(name)s - %(levelname)s - %(message)s', level=logging.INFO, encoding="utf-8"
)

connection = None

try:
    connection = psycopg2.connect(user="postgres",
                                password="Qq12345",
                                host="192.168.133.138",
                                port="5432", 
                                database="mydatabase")

    cursor = connection.cursor()
    cursor.execute("SELECT * FROM users;")
    data = cursor.fetchall()
    for row in data:
        print(row)  
    logging.info("Команда успешно выполнена")
except (Exception, Error) as error:
    logging.error("Ошибка при работе с PostgreSQL: %s", error)
finally:
    if connection is not None:
        cursor.close()
        connection.close()
```

### 2.2 Операция Insert

```python
import logging
import psycopg2
from psycopg2 import Error

logging.basicConfig(
    filename='app.log', format='%(asctime)s - %(name)s - %(levelname)s - %(message)s', level=logging.INFO, encoding="utf-8"
)

connection = None

try:
    connection = psycopg2.connect(user="postgres",
                                password="Qq12345",
                                host="192.168.133.138",
                                port="5432", 
                                database="mydatabase")

    cursor = connection.cursor()
    cursor.execute("INSERT INTO users (name, surname) VALUES ('Сергей', 'Сергеев');")
    connection.commit()
    logging.info("Команда успешно выполнена")
except (Exception, Error) as error:
    logging.error("Ошибка при работе с PostgreSQL: %s", error)
finally:
    if connection is not None:
        cursor.close()
        connection.close()
        logging.info("Соединение с PostgreSQL закрыто")
```

Отличие от SELECT заключается в том, что мы сохраняем изменения через `connection.commit()`

### 2.3 Операция Update

```python
import logging
import psycopg2
from psycopg2 import Error

logging.basicConfig(
    filename='app.log', format='%(asctime)s - %(name)s - %(levelname)s - %(message)s', level=logging.INFO, encoding="utf-8"
)

connection = None

try:
    connection = psycopg2.connect(user="postgres",
                                password="Qq12345",
                                host="192.168.133.138",
                                port="5432", 
                                database="mydatabase")

    cursor = connection.cursor()
    cursor.execute("Update users set name = 'Яков' where id = 1;")
    connection.commit()
    logging.info("Команда успешно выполнена")
except (Exception, Error) as error:
    logging.error("Ошибка при работе с PostgreSQL: %s", error)
finally:
    if connection is not None:
        cursor.close()
        connection.close()
        logging.info("Соединение с PostgreSQL закрыто")
```

### 2.4 Операция Delete

```python
import logging
import psycopg2
from psycopg2 import Error

logging.basicConfig(
    filename='app.log', format='%(asctime)s - %(name)s - %(levelname)s - %(message)s', level=logging.INFO, encoding="utf-8"
)

connection = None

try:
    connection = psycopg2.connect(user="postgres",
                                password="Qq12345",
                                host="192.168.133.138",
                                port="5432", 
                                database="mydatabase")

    cursor = connection.cursor()
    cursor.execute("Delete from users where name = 'Сергей';")
    connection.commit()
    logging.info("Команда успешно выполнена")
except (Exception, Error) as error:
    logging.error("Ошибка при работе с PostgreSQL: %s", error)
finally:
    if connection is not None:
        cursor.close()
        connection.close()
        logging.info("Соединение с PostgreSQL закрыто")
```

