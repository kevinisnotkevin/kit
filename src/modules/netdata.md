# Обработка данных через Интернет
1. [email - Пакет обработки электронной почты и MIME](#1)
2. [json - Кодер и декодер JSON](#2)
3. [mailbox - Работа с почтовыми ящиками различных форматов](#3)
4. [mimetypes - Сопоставление имен файлов с типами MIME](#4)
5. [base64 - Кодировки данных Base16, Base32, Base64, Base85](#5)
6. [binascii - Преобразование между двоичным кодом и ASCII](#6)
7. [quopri - Кодирование и декодирование MIME-данных, пригодных для печати](#7)


## <div id="1">1. email - Пакет обработки электронной почты и MIME</div>
> Модуль предоставляет функциональность для работы с email. Используется для создания, отправки, получения и обработки писем.
### Использование
- Создание email с заголовком, телом, вложениями и тд.
- Отправка email через SMTP (Simple Mail Transfer Protocol) с использованием модуля `smptlib`.
- Работа с полученными email (извлечение заголовков, тела письма, вложений и тд.).
- Манипуляции с MIME (Multipurpose Internet Mail Extensions). Используется для передачи сообщений, файлов, изображений.
- Автоматизация отправки отчетов по email, обработка входящих писем.
### Примеры
Отправка простого текстового письма:
```python
# Создание объекта письма
msg = MIMEText("Привет, это тестовое письмо!")

# Заполнение заголовков
msg["Subject"] = "Тестовое письмо"
msg["From"] = "your_email@example.com"
msg["To"] = "recipient@example.com"

# Отправка письма через SMTP
with smtplib.SMTP("smtp.example.com", 587) as server:
    server.login("your_email@example.com", "your_password")
    server.sendmail("your_email@example.com", "recipient@example.com", msg.as_string())
```


## <div id="2">2. json - Кодер и декодер JSON</div>
> Модуль предоставляет функции для работы с форматом JSON. JSON (JavaScript Object Notation) - формат обмена данными, основанный на синтаксисе JS. Представляет собой текстовый формат обмена данными, который легко читается.
### Использование
- Сериализация обьектов Python в строку JSON для передачи данных между веб-сервером и клиентом.
- Десериализация строки JSON в обьекты Python.
- Чтение, запись JSON в файл.
### Примеры
Сериализация:
```python
data = {
    "name": "John",
    "age": 30,
    "city": "New York"
}
json_string = json.dumps(data)
```
Десериализация:
```python
json_string = '{"name": "John", "age": 30, "city": "New York"}'
data = json.loads(json_string)
```
Чтение и запись в файл:
```python
data = {
    "name": "John",
    "age": 30,
    "city": "New York"
}

# Запись в файл
with open('data.json', 'w') as json_file:
    json.dump(data, json_file)

# Чтение из файла
with open('data.json', 'r') as json_file:
    loaded_data = json.load(json_file)
    print(loaded_data)
```


## <div id="3">3. mailbox - Работа с почтовыми ящиками различных форматов</div>
> Модуль предоставляет классы для доступа и управления почтовыми ящиками на диске и сообщениями, которые они содержат. Поддерживаемые форматы почтовых ящиков: Maildir, mbox, MH, Babyl и MMDF.
### Использование
- Управление почтовыми ящиками.
- Создание новых почтовых ящиков.
### Примеры
```python
mbox = mailbox.mbox('example.mbox')
for message in mbox:
    print(message['From'])
    print(message['Subject'])
    # Другие операции с сообщением
```


## <div id="4">4. mimetypes - Сопоставление имен файлов с типами MIME</div>
> Модуль предоставляет функции и класс для преобразования имени файла или URL-адреса в тип MIME (Multipurpose Internet Mail Extensions), связанного с расширением имени файла.
### Использование
- Определение MIME-типа файла по его расширению.
- Получение расширения файла по MIME-типу.
- Загрузка пользовательских типов MIME из файла.
- Используется для обработки HTTP-заголовков и определения типов файлов при их загрузке или отправке.
### Примеры
```python
file_name = 'example.txt'
mime_type, encoding = mimetypes.guess_type(file_name)


mime_type = 'text/plain'
extension = mimetypes.guess_extension(mime_type)


mimetypes.read_mime_types('custom_mime.types')
```


## <div id="5">5. base64 - Кодировки данных Base16, Base32, Base64, Base85</div>
> Модуль предоставляет функции для кодирования двоичных данных в печатные символы ASCII и декодирования таких кодировок обратно в двоичные данные. Кодировки подходят для кодирования двоичных данных, чтобы их можно было безопасно отправлять по email, использовать как часть URL-адресов или включать в состав запроса HTTP POST. Это способ представления двоичных данных в текстовом виде (полезно при передаче бинарных данных в текстовых протоколах, например, XML, JSON).
### Использование
- Кодирование данных.
- Декодирование данных.
- Использование в URL.
### Примеры
```python
data = b'Hello, World!'
encoded_data = base64.b64encode(data)


encoded_data = b'SGVsbG8sIFdvcmxkIQ=='
decoded_data = base64.b64decode(encoded_data)


data = b'Hello, World!'
url_safe_encoded_data = base64.urlsafe_b64encode(data)
```


## <div id="6">6. binascii - Преобразование между двоичным кодом и ASCII</div>
> Модуль предоставляет функции для преобразования между двоичными и различными двоичными представлениями в кодировке ASCII. Обычно эти функции используются через `uu` или `base64`. Содержит функции низкого уровня, написанные на Си для большей скорости, которые используются модулями более высокого уровня.
### Использование
- Преобразование бинарных данных в шестадцатеричное (hex) представление.
- Преобразование шестадцатеричного представления в бинарные данные.
- Кодирование бинарных данных в base64.
- Декодирование строки base64 в бинарные данные.
### Примеры
```python
binary_data = b'Hello, world!'
hex_representation = binascii.hexlify(binary_data)


hex_string = b'48656c6c6f2c20776f726c6421'
binary_data = binascii.unhexlify(hex_string)


binary_data = b'Hello, world!'
base64_representation = binascii.b2a_base64(binary_data)



base64_string = b'SGVsbG8sIHdvcmxkIQo='
binary_data = binascii.a2b_base64(base64_string)
```


## <div id="7">7. quopri - Кодирование и декодирование MIME-данных, пригодных для печати</div>
> Модуль предоставляет функции для кодирования и декодирования данных в формате quoted-printable (это метод кодирования, который используется для представления непечатаемых символов или специальных символов в виде ASCII-символов). Этот формат используется в email, чтобы обеспечить правильное отображение текста с использованием различных символов.
### Использование
- Правильное представление данных в ASCII-символах.
### Примеры
```python
original_text = "Привет, мир!"
encoded_text = quopri.encodestring(original_text.encode('utf-8')).decode('utf-8')


encoded_text = "=?utf-8?b?0J/RgNC40LLQtdGCIQ==?="
decoded_text = quopri.decodestring(encoded_text.encode('utf-8')).decode('utf-8')

```
