# Обработка структурированной разметки
1. [html - Поддержка языка гипертекстовой разметки](#1)
2. [Модули обработки XML](#2)


## <div id="1">1. html - Поддержка языка гипертекстовой разметки</div>
> Модуль предоставляет функции, субмодуль `html.parser` (для анализа текстовых файлов, отформатированных в HTML и XHTML), субмодуль `html.entities` (четыре словаря: html5, name2codepoint, codepoint2name, entitydefs) для работы с HTML-данными.
### Использование
- Экранирование спецсимволов в HTML.
- Генерация HTML-кода из данных Python.
- Разбор и обработка HTML-кода.
### Примеры
Экранирование спецсимволов:
```python
original_text = '<p>This is a paragraph</p>'
escaped_text = html.escape(original_text)
```
Генерация HTML-кода из данных:
```python
div_content = 'This is inside the div tag'
html_code = f'<div>{html.escape(div_content)}</div>'
```
Извлечение данных из HTML-страницы:
```python
from html.parser import HTMLParser

class MyHTMLParser(HTMLParser):
    def handle_starttag(self, tag, attrs):
        print(f"Encountered a start tag: {tag}")

parser = MyHTMLParser()
html_code = '<p>This is a paragraph</p>'
parser.feed(html_code)
```


## <div id="2">2. Модули обработки XML</div>
> Модуль предоставляет подмодули `xml.entree.ElementTree` (XML-процессор), `xml.dom` (определение DOM API), `xml.sax` (Базовые классы SAX2 и функции) и `xml.parsers.expat` (привязка парсера Expat) для работы с XML-данными (Extensible Markup Language). Модули обработки XML не защищены от вредоносно созданных данных. Злоумышленник может злоупотреблять функциями XML для проведения атак типа «отказ в обслуживании», доступа к локальным файлам, создания сетевых подключений к другим машинам или обхода брандмауэров.
### Использование
- Чтение XML-данных.
- Запись XML-данных.
- Обработка XML-данных.
### Примеры
```python
import xml.etree.ElementTree as ET

# Чтение XML-данных из файла
tree = ET.parse('example.xml')
root = tree.getroot()

# Навигация по элементам и вывод значений
for child in root:
    print(f"{child.tag}: {child.text}")


# Создание XML-документа
root = ET.Element("root")
child = ET.SubElement(root, "child")
child.text = "Hello, XML!"

# Создание объекта ElementTree и сохранение в файл
tree = ET.ElementTree(root)
tree.write("example.xml")
```
