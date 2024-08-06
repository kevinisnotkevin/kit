# Сжатие и архивирование данных
1. [zlib - Сжатие, совместимое с gzip](#1)
2. [gzip - Поддержка файлов gzip](#2)
3. [bz2 - Поддержка сжатия bzip2](#3)
4. [lzma - Сжатие с использованием алгоритма LZMA](#4)
5. [zipfile - Работа с ZIP-архивами](#5)
6. [tarfile - Чтение и запись файлов tar-архива](#6)


## <div id="1">1. zlib - Сжатие, совместимое с gzip</div>
> Модуль предоставляет функции для сжатия и распаковки данных с использованием алгоритма сжатия zlib.
### Использование
- Работа с большими обьемами информации или при передаче данных по сети.
### Примеры
```python
# Сжатие байтовых данных.
data = b"Hello, this is some data to compress."
compressed_data = zlib.compress(data)


# Распаковка сжатых данных.
compressed_data = b"x\x9c\xf3H\xcd\xc9\xc9W(\xcf/\xcaIQ\xcc \x82\r\x00\xbd\r"
decompressed_data = zlib.decompress(compressed_data)


# Считывание сжатых данных из файла и распаковка.
with open("compressed_file.gz", "rb") as f:
    compressed_data = f.read()

decompressed_data = zlib.decompress(compressed_data)
```


## <div id="2">2. gzip - Поддержка файлов gzip</div>
> Модуль предоставляет класс и функции для работы с файлами и данными в формате gzip (.gz), который используется для сжатия данных.
### Использование
- Для работы с HTTP-заголовками сжатия (Сервер может отправлять данные с заголовком `Content-Encoding: gzip`, указывая, что данные сжаты).
### Примеры
```python
# Сжатие файлов.
with open('example.txt', 'rb') as f_in:
    with gzip.open('example.txt.gz', 'wb') as f_out:
        f_out.writelines(f_in)


# Чтение данных из сжатых файлов.
with gzip.open('example.txt.gz', 'rb') as f:
    data = f.read()


# Сжатие и декомпрессия данных, не записывая в файл.
data = b"some data to compress"
# Сжатие данных
compressed_data = gzip.compress(data)
# Декомпрессия данных
decompressed_data = gzip.decompress(compressed_data)
```


## <div id="3">3. bz2 - Поддержка сжатия bzip2</div>
> Модуль предоставляет классы и функции для сжатия и распаковки данных с использованием алгоритма сжатия bzip2 (Высокая степень сжатия данных).
### Использование
- Сжатие файлов.
- Сжатие и декомпрессия данных в памяти.
### Примеры
```python
# Сжатие и уменьшение размера файлов на диске.
with open('исходный_файл.txt', 'rb') as source_file:
    with bz2.BZ2File('сжатый_файл.bz2', 'wb') as compressed_file:
        compressed_file.write(source_file.read())


# Сжатие и декомпрессия данных в памяти.
data_to_compress = b'Исходные данные для сжатия'
compressed_data = bz2.compress(data_to_compress)
decompressed_data = bz2.decompress(compressed_data)
```


## <div id="4">4. lzma - Сжатие с использованием алгоритма LZMA</div>
> Модуль предоставляет классы и функции для сжатия и распаковки данных с использованием алгоритма сжатия LZMA (Высокий уровень сжатия данных). Похоже на модуль `bz2`.
### Использование
- Сжатие данных для экономии места или ускорение передачи данных.
### Примеры
```python
# Создание архива с меньшим обьемом данных.
with open('example.txt', 'rb') as f_in:
    with lzma.open('example.xz', 'wb') as f_out:
        f_out.write(f_in.read())


# Сжатие данных в памяти.
data = b'This is some data to compress.'
compressed_data = lzma.compress(data)


# Работа с архивами.
with lzma.LZMAFile('example.xz', 'rb') as f:
    decompressed_data = f.read()
```


## <div id="5">5. zipfile - Работа с ZIP-архивами</div>
> Модуль предоставляет инструменты для создания, чтения, записи, добавления, вывода ZIP-файла. Может обрабатывать ZIP-файлы, использующие ZIP64 расширение (т.е. размером не более 4ГиБ). Поддерживает расшифровку зашифрованных файлов в ZIP-архивах, но не может создать зашифрованных файл. Расшифровка происходит медленно, так как реализация на Python, а не на Си.
### Использование
- Работа с ZIP архивами.
### Примеры
```python
# Извлечение файлов из существующего ZIP-архива.
with zipfile.ZipFile('example.zip', 'r') as myzip:
    myzip.extractall('extracted_folder')


# Добавление нового файла в существующий ZIP-архив.
with zipfile.ZipFile('example.zip', 'a') as myzip:
    myzip.write('new_file.txt')


# Получение информации о содержимом ZIP-архива.
with zipfile.ZipFile('example.zip', 'r') as myzip:
    file_list = myzip.namelist()
    file_info = myzip.getinfo('file.txt')
```


## <div id="6">6. tarfile - Чтение и запись файлов tar-архива</div>
> Модуль предоставляет возможность читать и записывать tar-архивы, в том числе использующие сжатие gzip, bz2, lzma.
### Использование
- Чтение, запись `.zip` файлов.
### Примеры
```python
# Создание TAR-архива.
with tarfile.open('example.tar', 'w') as tar:
    tar.add('file1.txt')
    tar.add('file2.txt')


# Извлечение файлов из TAR-архива.
with tarfile.open('example.tar', 'r') as tar:
    tar.extractall()


# Добавление файлов в существующий TAR-архив.
with tarfile.open('example.tar', 'a') as tar:
    tar.add('file3.txt')


# Получение списка файлов в TAR-архиве.
with tarfile.open('example.tar', 'r') as tar:
    file_list = tar.getnames()
    print(file_list)
```


