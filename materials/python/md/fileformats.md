# Форматы файлов
1. [csv - Чтение и запись CSV-файлов](#1)
2. [configparser - Парсер конфигурационного файла](#2)
3. [tomllib - Разбор файлов TOML](#3)
4. [netrc - обработка файлов netrc](#4)
5. [plistlib - Генерация и анализ .plist файлов Apple](#5)


## <div id="1">1. csv - Чтение и запись CSV-файлов</div>
> Модуль предоставляет классы для чтения и записи табличных данных в формате CSV (значения, разделенные запятыми).
### Использование
- Запись, чтение данных в формате, предпочитаемом Excel.
### Примеры
```python
# Создание обьекта, который читает строки из CSV-файла.
with open('file.csv', 'r') as file:
    csv_reader = csv.reader(file)
    for row in csv_reader:
        print(row)


# Создание обьекта для записи данных в CSV-файл.
with open('output.csv', 'w', newline='') as file:
    csv_writer = csv.writer(file)
    csv_writer.writerow(['Name', 'Age', 'City'])
    csv_writer.writerow(['John', '25', 'New York'])


# Получение словаря для каждой строки (аналог csv.reader()).
with open('file.csv', 'r') as file:
    csv_reader = csv.DictReader(file)
    for row in csv_reader:
        print(row)


# Работа с данными в виде словарей (аналог csv.writer()).
with open('output.csv', 'w', newline='') as file:
    fieldnames = ['Name', 'Age', 'City']
    csv_writer = csv.DictWriter(file, fieldnames=fieldnames)
    csv_writer.writeheader()
    csv_writer.writerow({'Name': 'John', 'Age': '25', 'City': 'New York'})
```


## <div id="2">2. configparser - Парсер конфигурационного файла</div>
> Модуль предоставляет класс, реализующий базовый язык конфигурации, обеспечивающий структуру.
### Использование
- Написание программ, которые конечные пользователи могут легко настроить.
- Работа с INI-файлами, которые представляют простой текстовый формат для хранения параметров конфигурации.
### Примеры
```python
# Создаем объект ConfigParser
config = configparser.ConfigParser()

# Читаем INI-файл
config.read('example.ini')

# Получаем значение параметра
value1 = config.get('Section1', 'key1')
print(value1)  # Выводит: value1

# Задаем новое значение параметра
config.set('Section1', 'key1', 'new_value1')

# Записываем изменения обратно в INI-файл
with open('example.ini', 'w') as config_file:
    config.write(config_file)
```


## <div id="3">3. tomllib - Разбор файлов TOML</div>
> Модуль предоставляет функции для анализа TOML (язык Тома). Не поддерживает запись TOML.
### Использование
- Работа с TOML данными.
### Примеры
```python
# Разбор файла TOML:
with open("pyproject.toml", "rb") as f:
    data = tomllib.load(f)


# Разбор строки TOML:
toml_str = """
python-version = "3.11.0"
python-implementation = "CPython"
"""
data = tomllib.loads(toml_str)
```


## <div id="4">4. netrc - обработка файлов netrc</div>
> Модуль предоставляет класс для анализа и инкапсуляции формата файла netrc, используемого программой FTP Unix и другими FTP-клиентами.
### Использование
- Хранение логинов и паролей, чтобы автоматизировать процесс аутентификации при доступе к удаленным ресурсам.


## <div id="5">5. plistlib - Генерация и анализ .plist файлов Apple</div>
> Модуль предоставляет функции для чтения и записи файлов списка свойств, используемых Apple.
### Использование
- Запись, анализ файлов .plist.
