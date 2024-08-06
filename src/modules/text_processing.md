### string
`string` предоставляет возможность выполнять сложные замены переменных и форматирование значений с помощью `format()` метода. Класс `Formatter` позволяет создавать и настраивать собственные способы форматирования строк. Класс `Template` принимает сроку и позволяет интернационализировать ее.
- **`ascii_letters`**: Обьединяет `ascii_lowercase` и `ascii_lowercase`.
- **`ascii_lowercase`**: Строка *'abcdefghijklmnopqrstuvwxyz'*.
- **`ascii_uppercase`**: Строка *'ABCDEFGHIJKLMNOPQRSTUVWXYZ'*.
- **`digits`**: Строка *'0123456789'*.
- **`hexdigits`**: Строка *'0123456789abcdefABCDEF'*.
- **`octdigits`**: Строка *'01234567'*.
- **`punctuation`**: Строка символов пунктуации ASCII *'!"#$%&'()*+,-./:;<=>?@[\\]^_{|}~'*.
- **`printable`**: Строка печатуемых символов ASCII (`gidits`, `ascii_letters`, `punctuation`, `whitespace`).
- **`whitespace`**: Строка пробелов ASCII (пробелы, табуляция, перевод строки, возврат, перевод страницы, вертикальная табуляция).
```python
from string import Template
t = Template('$atext new text $b.')
t.substitute(a='New', b='correct')
```


### re
`re` предоставляет инструменты регулярных выражений для расширенной обработки строк. Предлагает краткие и оптимизированные решения для сложных сопоставлений и манипуляций.

Символы регулярных выражений:
Symbol | Matches
--- | ---
`?` | 0 или 1 повторение предыдущего шаблона.
`*` | 0 или более повторений предыдущего шаблона.
`+` | 1 и более повторений предыдущего шаблона.
`{n}` | n повторений предыдущего шаблона.
`{n,}` | От n и более повторений предыдущего шаблона.
`{,m}` | От 0 до n повторений предыдущего шаблона.
`{n,m}` | Не менее n и не более m.
`{n,m}?` | Нежадное сопоставление предыдущего шаблона.
`*?` | Нежадное сопоставление предыдущего шаблона.
`+?` | Нежадное сопоставление предыдущего шаблона.
`^spam` | Строка должна начинаться spam.
`spam$` | Строка должна заканчиваться spam.
`.` | Любой символ, кроме символов новой строки.
`\d` | Цифра.
`\w` | Слово.
`\s` | Пробел.
`\D` | Что угодно, кроме цифры.
`\W` | Что угодно, кроме слова.
`\S` | Что угодно, кроме пробела.
`[abc]` | Любой символ в скобках.
`[^abc]` | Любой символ, не заключенный в скобки.
Методы:
- **`compile`**: Компилирует шаблон регулярного выражения в обьект регулярного выражения, который можно использовать с помощью `match`, `search`.
- **`search`**: Сканирует строку в поисках первого совпадения шаблона.
- **`match`**: Соответствие символов в начале строки шаблону.
- **`fullmatch`**: Соответствие строки шаблону.
- **`split`**: Разделяет строку по шаблону указанное количество раз и возвращает список.
- **`findall`**: Возвращает все совпадения шаблона в строке в виде списка строк или кортежей.
- **`finditer`**: Возвращает итератор, возвращающий `Match` обьекты по всем совпадениям шаблона в строке.
- **`sub`**: Возвращает строку, полученную путем замены крайних левых вхождений шаблона в строке.
- **`subn`**: Выполняет то же, что и `sub`, но возвращает кортеж.
- **`escape`**: Экранирует специальные символы в шаблоне.
- **`purge`**: Очищает кеш регулярных выражений.
```python
# Поиск всех совпадений
phone_num_regex = re.compile(r'\d\d\d-\d\d\d-\d\d\d\d') # has no groups
phone_num_regex.findall('Cell: 415-555-9999 Work: 212-555-0000')  # ['415-555-9999', '212-555-0000']

# Сопоставление без учета регистра:
robocop = re.compile(r'robocop', re.I)
robocop.search('Robocop is part man, part machine, all cop.').group()  # 'Robocop'
robocop.search('ROBOCOP protects the innocent.').group()  # 'ROBOCOP'
robocop.search('Al, why does your programming book talk about robocop so much?').group()  # 'robocop'


# Замена строк:
names_regex = re.compile(r'Agent \w+')
names_regex.sub('CENSORED', 'Agent Alice gave the secret documents to Agent Bob.')  # 'CENSORED gave the secret documents to CENSORED.'


# Распределение регулярного выражения на несколько строк с помощью VERBOSE:
phone_regex = re.compile(r'((\d{3}|\(\d{3}\))?(\s|-|\.)?\d{3}(\s|-|\.)\d{4}(\s*(ext|x|ext.)\s*\d{2,5})?)')
# Становится:
phone_regex = re.compile(r'''(
    (\d{3}|\(\d{3}\))?            # area code
    (\s|-|\.)?                    # separator
    \d{3}                         # first 3 digits
    (\s|-|\.)                     # separator
    \d{4}                         # last 4 digits
    (\s*(ext|x|ext.)\s*\d{2,5})?  # extension
    )''', re.VERBOSE)
```


## <div id="3">3. difflib - Помощники для вычисления дельт</div>
Предоставляет классы и функции для сравнения последовательностей.
#### Классы и функции
##### class difflib.SequenceMatcher
Сравнивает пары при условии, что элементы последовательности хешируемы.
```python
>>> s1 = 'Hello world!'
>>> s2 = 'Hello peace!'
>>> d = difflib.SequenceMatcher(None, s1, s2)
>>> d.ratio()
0.5833333333333334
>>> d.get_opcodes()
[('equal', 0, 6, 0, 6), ('replace', 6, 11, 6, 11), ('equal', 11, 12, 11, 12)]
>>> d.get_matching_blocks()
[Match(a=0, b=0, size=6), Match(a=11, b=11, size=1), Match(a=12, b=12, size=0)]
```
##### class difflib.Differ
Сравнивает последовательность строк текста и создания удобочитаемых различий дельт.
Каждая строка начинается с буквенного кода:
Код | Значение
--- | ---
`- ` | строка, уникальная для последовательности 1
`+ ` | строка, уникальная для последовательности 2
` ` | линия, общая для обеих последовательностей
`? ` | строка отсутствует ни в одной входной последовательности


```python
>>> s1 = 'Hello world!'
>>> s2 = 'Hello peace!'
>>> d = difflib.Differ()
>>> d.compare(s1, s2)
>>> ''.join(d.compare(s1, s2))
'  H  e  l  l  o   - w- o- r- l- d+ p+ e+ a+ c+ e  !'
```
##### class difflib.HtmlDiff
Создает HTML-таблицу, показывающую параллельное, построчное сравнение текста с выделением межстрочных и внутристрочных интервалов.
__\_\_init\_\_(tabsize=8, wrapcolumn=None, linejunk=None, charjunk=IS_CHARACTER_JUNK)__ - инициализирует HtmlDiff.  `tabsize` - расстояние между позициями табуляции, `wrapcolumn` - номер столбца, в котором строки развываются и переносятся (по умолчанию строки не переносятся), `linejunk` и `charjunk` - ключевые слова `ndiff()`.
__make_file(fromlines, tolines, fromdesc='', todesc='', context=False, numlines=5, *, charset='utf-8')__ - сравнивает `fromlines` и `tolines` и возвращает строку HTML, содержащую таблицу с различиями. `fromdesc` и `todesc` - строки заголовков столбцов из/в файл, `context` - показывать ли контекстуальные различия или полные файлы, `numlines` - контролирует количество строк, которые отображаются перед выделением различия при использовании гиперссылок.
__make_table(fromlines, tolines, fromdesc='', todesc='', context=False, numlines=5)__ - Сравнивает `fromlines` и `tolines` и возвращает строку HTML, содержащую построчные различия с выделенными межстрочными и внутристрочными изменениями.
```python
>>> s1 = 'Hello world!'
>>> s2 = 'Hello peace!'
>>> d = difflib.HtmlDiff()
>>> d.make_file(s1, s2)
```
##### difflib.context_diff(a, b, fromfile='', tofile='', fromfiledate='', tofiledate='', n=3, lineterm='\n')
Сравнивает `a` и `b`. Возвращает генератор, генерирующий дельта-линии в формате контекстного различия (только измененные строки + несколько строк контекста).
```python
>>> s1 = 'Hello world!'
>>> s2 = 'Hello peace!'
>>> d = difflib.context_diff(s1.splitlines(), s2.splitlines())
>>> {i for i in d}
{'--- \n', '***************\n', '--- 1 ----\n', '! Hello peace!', '! Hello world!', '*** \n', '*** 1 ****\n'}
```
##### difflib.get_close_matches(word, possibilities, n=3, cutoff=0.6)
Возвращает список лучших совпадений по слову или фразе. `word` - последовательность, для которой желательны близкие совпадения, `possibilities` - список последовательностей с которыми сопоставляется `word`, `n` - максимальное количество возвращаемых близких совпадений, `cutoff` - пороговое значение схожести.
```python
>>> difflib.get_close_matches('appel', ['ape', 'apple', 'peach', 'puppy'])
['apple', 'ape']
```
##### difflib.ndiff(a, b, linejunk=None, charjunk=IS_CHARACTER_JUNK)
Сравнивает `a` и `b`. Возвращает генератор дельта-линий. `linejunk` - функция, которая принимает строковый аргумент и возвращает `True`, если строка мусорная, иначе `False`, `charjunk` - функция, которая принимает символ и возвращает значение, если символ является мусорным, иначе `False`.
```python
>>> diff = ndiff('one\ntwo\nthree\n'.splitlines(keepends=True), 'ore\ntree\nemu\n'.splitlines(keepends=True))
>>> print(''.join(diff), end="")
- one
?  ^
+ ore
?  ^
- two
- three
?  -
+ tree
+ emu
```
##### difflib.restore(sequence, which)
Возвращает одну из двух последовательностей, сгенерировавших дельту.
```python
>>> diff = ndiff('one\ntwo\nthree\n'.splitlines(keepends=True), 'ore\ntree\nemu\n'.splitlines(keepends=True))
>>> diff = list(diff)
>>> print(''.join(restore(diff, 1)), end="")
one
two
three
>>> print(''.join(restore(diff, 2)), end="")
ore
tree
emu
```
##### difflib.unified_diff(a, b, fromfile='', tofile='', fromfiledate='', tofiledate='', n=3, lineterm='\n')
Сравнивает `a` и `b`. Возвращает генератор дельта-линий в едином формате различий.
##### difflib.diff_bytes(dfunc, a, b, fromfile=b'', tofile=b'', fromfiledate=b'', tofiledate=b'', n=3, lineterm=b'\n')
Сравнивает `a` и `b` с помощью `dfunc` (например, `unified_diff()`, `context_diff()`). Возвращает последовательность дельта-строк в формате `dfunc`. Преобразовывает все входные данные в строку.
##### difflib.IS_LINE_JUNK(line)
Возвращает `True` для игнорируемых строк. `line` игнорируется, если строка пуста или содержит один `#`.
##### difflib.IS_CHARACTER_JUNK(ch)
Возвращает `True` для игнорируемых символов. Символ `ch` игнорируется, если является пробелом или табуляцией.
##### class difflib.SequenceMatcher(isjunk=None, a='', b='', autojunk=True)
`isjunk` - функция, которая принимает элемент последовательности и возвращает `True`, если элемент является мусорным, `a` и `b` - сравниваемые последовательности, `autojunk` - отключать ли автоматическую эвристику нежелательной почты.
##### class difflib.Differ(linejunk=None, charjunk=None)
`linejunk` - функция, которая принимает один строковый аргумент и возвращает `True`, если строка является мусорной, `charjunk` - фукнция, которая принимает аргумент из одного символа и возвращает `True`, если символ является мусорным.
__compare(a, b)__ - сравнивает две последовательности строк и генерирует дельту.


## <div id="4">4. textwrap - Перенос и заполнение текста</div>
> Модуль форматирует абзацы текста по заданной ширине.
Предоставляет фукнции и класс `TextWrapper` для форматирования текста по ширине экрана или определенному количеству символов.
#### Функции
##### textwrap.wrap(text, width=70, *, initial_indent='', subsequent_indent='', expand_tabs=True, replace_whitespace=True, fix_sentence_endings=False, break_long_words=True, drop_whitespace=True, break_on_hyphens=True, tabsize=8, max_lines=None, placeholder=' [...]')
Обертывает один абзац `text`, поэтому каждая строка имеет длину не более `width`. Возвращает список выходных строк без последних символов новой строки.
##### textwrap.fill(text, width=70, *, initial_indent='', subsequent_indent='', expand_tabs=True, replace_whitespace=True, fix_sentence_endings=False, break_long_words=True, drop_whitespace=True, break_on_hyphens=True, tabsize=8, max_lines=None, placeholder=' [...]')
Обертывает один абзац в `text` и возвращает одну строку, содержащую абзац.
##### textwrap.shorten(text, width, *, fix_sentence_endings=False, break_long_words=True, break_on_hyphens=True, placeholder=' [...]')
Сворачивает и обрезает `text` по `width`.
##### textwrap.dedent(text)
Удаляет все общие начальные пробелы из каждой строки `text`.
##### textwrap.indent(text, prefix, predicate=None)
Добавляет `prefix` в начало выделенных строк `text`.
```python
doc = """The wrap() method is just like fill() except that it returns
a list of strings instead of one big string with newlines to separate
the wrapped lines."""
print(textwrap.fill(doc, width=40))
```

## <div id="5">5. unicodedata - База данных Юникод</div>
Предоставляет доступ к БД символов Юникода, которая определяет свойства символов для всех символов Юникода.


## <div id="6">6. stringprep - Подготовка интернет-строки</div>
Предоставляет функции, которые возвращают `True`, если параметр является частью набора.


## <div id="7">7. readline - Интерфейс чтения GNU</div>
Предоставляет функции для облегчения завершения чтения/записи файлов истории из интерпретатора.


## <div id="8">8. rlcompleter - Функция завершения для строки чтения GNU</div>
Предоставляет функцию завершения, подходящую для передачи `set_completer()` в `readline` модуль. Когда модуль импортируется на Unix платформу, автоматически создается `readline` экземпляр класса.