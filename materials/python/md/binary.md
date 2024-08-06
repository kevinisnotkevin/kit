# Двоичные данные
1. [struct](#struct): Интерпретирование байтов, как упакованные двоичные данные;
2. [__codecs__](#codecs): Реестр кодеков и базовые классы.


### struct
Модуль преобразует значения Python в структуры C, представленные как `bytes` обьекты Python. Эффективно при работе с бинарными данными, например, при взаимодействии с низкоуровневыми бинарными форматами файлов или сетевыми протоколами. Символы формата описывают какие данные упаковать или распаковать, управляют порядком, размером и выравниванием байтов. 
#### Использование
- Обмен данными между кодом Python и C.
- Обмен данными между приложениями с использованием согласованного макета данных.
#### Символы формата
Символ | Тип C | Тип Python | Размер | Описание
--- | --- | --- | --- | ---
`x` | pab byte | - | - | нулевой байт
`c` | char | bytes | 1 | символ
`b` | signed char | int | 1 | знаковый байт
`B` | unsigned char | int | 1 | беззнаковый байт
`?` | bool | bool | 1 | булево значение
`h` | short | int | 2 | короткое целое
`H` | unsigned short | int | 2 | короткое целое без знака
`i` | int | int | 4 | целое
`I` | unsigned int | int | 4 | целое без знака
`l` | long | int | 4 | длинное целое
`L` | unsigned long | int | 4 | длинное целое без знака
`q` | long long | int | 8 | длинное целое
`Q` | unsigned long long | - | 8 | длинное целое без знака
`n` | ssize_t | int | - | короткое целое без знака в сетевом порядке
`N` | size_t | int | - | длинное целое без знака в сетевом порядке
`e` | - | float | 2 | число с плавающей запятой одинарной точности
`f` | float | float | 4 | число с плавающей запятой
`d` | double | float | 8 | число с плавающей запятой двойной точности
`s` | char[] | bytes | - | строка (для указания размера строки, например - `10s`)
`p` | char[] | bytes | - | целое, представляющее указатель
`P` | void* | int | - | длинное целое без знака, представляющее указатель
#### Функции
#### struct.pack(format, v1, v2, ...)
Возвращает обьект байтов, содержащий значения `v1`, `v2`, `...` упакованный в соответствии с форматом `format`.
#### struct.pack_info(format, buffer, offset, v1, v2, ...)
Упаковывает значения `v1`, `v2`, `...` в соответствии с форматом `format` и записывает упакованные байтыв записываемый буфер `buffer`, начиная со смещения `offset` позиции.
#### struct.unpack(format, buffer)
Распаковывает из буфера `buffer` в соответствии с форматом `format`. Результатом является кортеж. Размер буфера в байтах должен соответствовать размеру, требуемому форматом.
#### struct.unpack_form(format, /, buffer, offset=0)
Распаковывает из буфера `buffer` в соответствии с форматом `format`. Результатом является кортеж. Размер буфера в байтах, начиная со смещения `offset` позиции, должен быть как минимум размером, требуемым форматом.
#### struct.iter_unpack(format, buffer)
Итеративно распаковывает из буфера `buffer` в соответствии с форматом `format`. Возвращает итератор, который будет читать из буфера куски одинакового размера до тех пор, пока все его содержимое не будет использовано. Размер буфера в байтах должен быть кратен размеру, требуемому форматом. Каждая итерация возвращает кортеж, указанный в строке формата.
#### struct.calcsize(format)
Возвращает размер структуры, соответствующей формату `format`.
#### Примеры
```python
struct.pack(">bhl", 1, 2, 3) # b'\x01\x00\x02\x00\x00\x00\x03'
struct.unpack(">bhl", b'\x01\x00\x02\x00\x00\x00\x03') # (1, 2, 3)
struct.calcsize(">bhl") # 7
struct.pack("@ccc", b'1', b'2', b'3') # b'123'
struct.pack("@3s", b'123') # b'123'
```


### codecs
Модуль для обработки различных кодировок текста. Предоставляет функции для работы с разными форматами кодирования.
#### Использование
- Чтение и запись файлов с определенной кодировкой. Эффективно, если файлы сохранены в различных кодировках.
- Преобразование текста между кодировками. Эффективно, если данные имеют различные кодировки и необходимо привести к одному формату.
- Работа с различными кодировками при работе с сетью. Эффективно, если данные из разных источников имеют разные кодировки.
#### Функции
#### codecs.encode(obj, encoding='utf-8', errors='strict')
#### codecs.decode(obj, encoding='utf-8', errors='strict')
#### codecs.lookup(encoding)
#### class codecs.CodecInfo(encode, decode, streamreader=None, streamwriter=None, incrementalencoder=None, incrementaldecoder=None, name=None)
#### codecs.getencoder(encoding)
#### codecs.getdecoder(encoding)
#### codecs.getincrementalencoder(encoding)
#### codecs.getincrementaldecoder(encoding)
#### codecs.getreader(encoding)
#### codecs.getwriter(encoding)
#### codecs.register(search_function)
#### codecs.unregister(search_function)
#### codecs.open(filename, mode='r', encoding=None, errors='strict', buffering=- 1)
#### codecs.EncodedFile(file, data_encoding, file_encoding=None, errors='strict')
#### codecs.iterencode(iterator, encoding, errors='strict', **kwargs)¶
#### codecs.iterdecode(iterator, encoding, errors='strict', **kwargs)
