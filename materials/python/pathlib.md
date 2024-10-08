# PATHLIB

> Модуль предоставляет классы для работы с путями к файлам и каталогам.

## Использование

- Создание, обьединение, манипулирование путями к файлам и каталогам.
- Чтение, запись данных в файл.
- Получение имени и расширения файла, родительского каталога.
- Итерация по содержимому каталогов.

## Примеры

```python
path = pathlib.Path("/путь/к/вашему/файлу.txt")

if path.exists():
    content = path.read_text()
    print(content)

parent_directory = path.parent

for file in parent_directory.iterdir():
    print(file)
```

## Использование обратного слэша в Windows и слэша в MacOS и Linux

В Windows имена папок разделяются обратной косой чертой. В macOS и Linux разделителем служит косая черта. Чтобы программы работали во всех операционных системах, нужно обрабатывались оба случая. Модуль pathlib решает эту проблему.

```python
from pathlib import Path

pth = (Path('spam', 'bacon', 'eggs'))
print((pth)) ## spam\\bacon\\eggs
print(type(pth)) ## <class 'pathlib.WindowsPath'>
```
- Команда `Path('spam’, ’bacon', ’eggs')` возвращает объект `WindowsPath` для строки пути: `WindowsPath('spam/bacon/eggs')`. Несмотря на то что в Windows разделителем служит обратная косая черта, представление объекта `WindowsPath` в интерактивной оболочке отображается с использованием косой черты, поскольку разработчики открытого программного обеспечения исторически предпочитают операционную систему Linux. В Linux функция `Path()` вернула бы объект `PosixPath`, а функция `str()` вернула бы строку 'spam/bacon/eggs'.

## Текущий рабочий каталог

Каждой программе, выполняемой на компьютере, назначается текущий каталог. Предполагается, что любые имена файлов и папок, которые не начинаются с указания корневой папки, заданы относительно текущего каталога.

Функция `Path.cwd()` возвращает объект текущего каталога. Сменить текущий каталог можно с помощью функции `os.chdir()`. Если попытаться перейти в несуществующий каталог, Python выдаст сообщение об ошибке.

```python
from pathlib import Path
import os

print((Path.cwd())) # D:\\Projects\\DevOps
os.chdir('C:\\\\Windows\\\\System32')
print((Path.cwd())) # C:\\Windows\\System32
```

В модуле pathlib не существует функции для смены текущего каталога. Это связано с тем, что изменение текущего каталога во время работы программы часто приводит к трудно обнаруживаемым ошибкам.

`os.getcwd()` - это устаревший способ получения имени текущего каталога в виде строки.

## Домашний каталог

```python
Path.home()
```
- Функция `Path.home()` возвращает объект `Path` домашнего каталога.

## Создание новых папок

```python
 import os
 os.makedirs('C:\\\\delicious\\\\walnut\\\\waffles')
```
- Создание новой папки с помощью функции `os.makedirs()`. В результате будет создана не только папка C:\delicious, но и расположенная в ней папка walnut, а также расположенная в папке C:\delicious\walnut папка waffles. Таким образом, функция `os.makedirs()` создает все необходимые промежуточные папки, гарантируя существование полного пути.

```python
Path(r'D:\\Projects\\New').mkdir()
```
- Чтобы создать каталог из объекта Path, вызовите для него метод `mkdir()`. Метод `mkdir()` способен за один раз создать всего один каталог. Он не может создавать несколько подкаталогов одновременно, в отличие от функции `os.makedirs()`.

## Обработка абсолютных и относительных путей

```python
print((Path.home().is_absolute())) # True
print((Path('spam/bacon/eggs1').is_absolute())) # False
```
- Модуль `pathlib` содержит методы, позволяющие проверить, является ли данный путь абсолютным, и получить абсолютный путь из относительного пути. Метод `is_absolute()` объекта `Path` вернет `True`, если объект представляет абсолютный путь, или `False`, если объект представляет относительный путь.

```python
print((Path.home().is_absolute())) # True
pth = Path.cwd() / Path('spam/bacon/eggs1') 
print(pth) # D:\\Projects\\DevOps\\spam\\bacon\\eggs1
print(pth.is_absolute()) # True
```
- Чтобы получить абсолютный путь на основе относительного, можно поместить `Path.cwd() /` перед объектом `Path` относительного пути. Если путь задан относительно какого-то другого пути, подставьте этот путь вместо `Path.cwd()`.

Модуль `os.path` тоже содержит несколько полезных функций для работы с абсолютными и относительными путями.
- Функция `os.path.abspath(путь)` возвращает строку абсолютного пути для заданного аргумента. Это удобный способ преобразовать относительный путь в абсолютный.
- Функция `os.path.isabs(путь)` возвращает True, если аргумент представляет абсолютный путь, и False, если аргумент - относительный путь.
- Функция `os.path.relpath(путь, начало)` возвращает строку относительного пути, который формируется от каталога начало до каталога путь. Если аргумент начало не задан, в качестве начальной точки используется текущий каталог.
