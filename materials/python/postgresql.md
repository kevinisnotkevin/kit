# asyncpg

## Основные методы

```python
conn = await asyncpg.connect(
	user='username',
	password='password',
	database='dbname',
	host='localhost',
	port='port'
)
# Или
conn = await asyncpg.connect('postgresql://user:pass@host:port/dbname')
```
- `connect`: Подключение к БД.

```python
rows = await conn.fetch('SELECT * FROM users')
for row in rows:
    print(row['id'], row['name'])
```
- `fetch`: Выполняет запрос и возвращает все строки как список (`list[Record]`).

```python
row = await conn.fetchrow('SELECT * FROM users WHERE id = $1', 1)
if row:
    print(row['id'], row['name'])
```
- `fetchrow`: Выполняет запрос и возвращает только одну строку (`Record`).

```python
count = await conn.fetchval('SELECT COUNT(*) FROM users WHERE age > $1', 18)
print(f"Количество пользователей старше 18 лет: {count}")
```
- `fetchval`: Выполняет запрос и возвращает одно значение из первой строки (`Any`).

```python
result = await conn.execute('INSERT INTO users(name, age) VALUES($1, $2)', 'Alice', 30)
print(result)  # INSERT 0 1
```
- `execute`: Выполняет запрос без возврата данных (`str`).

```python
await conn.copy_to_file('output.csv', 'COPY users TO STDOUT WITH CSV HEADER')
```
- `copy_to_file`: Копирует результат запроса в файл.

```python
await conn.copy_from_file('input.csv', 'COPY users FROM STDIN WITH CSV HEADER')
```
- `copy_from_file`: Копирует данные из файла в таблицу.

## Транзакции

```python
txn = await conn.start_transaction()
try:
    await conn.execute('UPDATE users SET age = $1 WHERE id = $2', 25, 1)
    await txn.commit()  # коммит транзакции
except:
    await txn.rollback()  # откат транзакции
```
- `start_transaction`: Создает объект транзакции.

```python
async with conn.transaction():
    await conn.execute('UPDATE users SET age = $1 WHERE id = $2', 25, 1)
```
- `transaction`: Открытие контекста для транзакции с автоматическим коммитом или откатом.

## Пулы соединений

```python
pool = await asyncpg.create_pool(
	user='username',
	password='password',
	database='dbname',
	host='localhost',
	port='port',
	min_size=1,  # Минимальное кол-во соединений в пуле
	max_size=10,  # Максимальное кол-во соединений в пуле
)
```
- `create_pool`: Создание пула соединений для многозадачности.

```python
async with pool.acquire() as conn:
    await conn.execute('SELECT * FROM users')
```
- `acquire`: Получение соединения из пула.

```python
await pool.release()
```
- `release`: Ручное освобождение соединения из пула (если нужно, обычно используется внутри `acquire`).

```python
await pool.close()
```
- `close`: Закрытие пула соединений.

## Дополнительно

```python
await conn.add_struct_type('my_type', ['field1', 'field2'])
```
- `add_struct_type`: Добавление нового типа данных в PostgreSQL.

```python
if conn.is_closed():
    print("Соединение закрыто")
```
- `is_closed`: Проверяет, закрыто ли соединение.

```python
await conn.reset()
```
- `reset`: Сбрасывает соединение, очищает состояние.

```python
await conn.set_type_codec('my_type', encoder=my_encoder, decoder=my_decoder, schema='public')
```
- `set_type_codec`: Настроить кодек для пользовательского типа данных.

```python
await conn.add_listener('my_channel', callback_function)
```
- `add_listener`: Подписка на канал для получения уведомлений.

```python
await conn.remove_listener('my_channel', callback_function)
```
- `unlisten`: Отписка от канала.

```python
await conn.set_client_encoding('UTF8')
```
- `set_client_encoding`: Устанавливает кодировку клиента для соединения.

```python
plan = await conn.explain('SELECT * FROM users WHERE age > 30')
print(plan)
```
- Получение плана выполнения запроса.

```python
try:
    await conn.execute('INSERT INTO users (name) VALUES ($1)', 'John')
except asyncpg.exceptions.UniqueViolationError as e:
    print(f"Ошибка уникальности: {e}")
```
- `asyncpg.exceptions.UniqueViolationError`: Ошибка уникальности.
- `asyncpg.exceptions.ForeignKeyViolationError`: Ошибка внешнего ключа.
- `asyncpg.exceptions.CheckViolationError`: Нарушение ограничения проверки.

```python
stmt = await conn.prepare('SELECT * FROM users WHERE age > $1')
rows = await stmt.fetch(20)
```
- `prepare`: Подготовка SQL-запроса для многократного использования (используется с параметрами).

```python
async for row in conn.map('SELECT * FROM users', some_function):
    print(row)
```
- `map`: Применяет функцию ко всем строкам результата запроса.

## Pool

**Пул соединений** - это механизм управления множеством соединений с БД, который позволяет многократно использовать заранее созданные соединения вместо создания новго соединения для каждого запроса.

**Преимущества**

- Ускорение работы, благодаря заранее установленным соединениям.
- Уменьшение кол-ва одновременных соединений с БД.
- Управление большим кол-вом запросов от пользователей, благодаря равномерному распределению их между доступными соединениями.

**Принцип работы**

- При создании пула создается минимальное и максимальное кол-во соединений. Пул заранее создает несколько соединений и держит их открытыми.
- Когда приложению нужно взаимодействовать с БД, оно запрашивает соединение из пула. Если свободное соединение доступно, оно передается приложению.
- Приложение выполняет запросы к БД через предоставленное соединение.
- После завершения работы с соединением оно возвращается обратно в пул и становится доступным для других запросов.
- Если кол-во активных соединений достигает максимального значения, то последующие запросы ждут освобождения соединений. Если соединения долгое время не используются, их число может уменьшаться до минимального уровня (Если включен таймаут).

# sqlalchemy

**sqlalchemy** - это библиотека для работы с БД (Postgresql, MySQL, SQLite, Oracle).

- Включает два компонента: core, ORM
- core предоставляет низкоуровневый доступ к БД для работы с sql запросами через обьектную модель и ручного создания запросов
- ORM предоставляет возможность работать через ООП

# Сравнение модулей

Модуль `psycopg2` подходит для большинства приложений, имеет высокую производительность, прост в настройке.

Модуль `asyncpg` подходит для асинхронных задач, быстрее чем `psycopg2`, но сложнее в настройке.

Модуль `SQLAlchemy` подходит для сложных приложений, имеет ORM, среднюю производительность, не сложен в настройке.

# Psycopg2

**Psycopg** - это модуль для работы с БД PostgreSQL, написанный на C.

Его основными особенностями являются полная реализация спецификации Python DB API 2.0 и потокобезопасность (несколько потоков могут использовать одно и то же соединение). Он был разработан для многопоточных приложений, которые создают и уничтожают множество курсоров и выполняют большое количество одновременных операций INSERT или UPDATE.

```
pip install psycopg2
```
- Установка модуля.

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
cursor = connection.cursor()
cursor.execute("SELECT * FROM users;")
data = cursor.fetchall()
for row in data:
	print(row)  
```
- Операция select.

```python
cursor = connection.cursor()
cursor.execute("INSERT INTO users (name, surname) VALUES ('Сергей', 'Сергеев');")
connection.commit()
```
- Операция insert. Отличие от SELECT заключается в том, что мы сохраняем изменения через `connection.commit()`

```python
cursor = connection.cursor()
cursor.execute("Update users set name = 'Яков' where id = 1;")
connection.commit()
```
- Операция update.

```python
cursor = connection.cursor()
cursor.execute("Delete from users where name = 'Сергей';")
connection.commit()
```
- Операция delete.