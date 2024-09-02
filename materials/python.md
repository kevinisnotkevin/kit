# Python Cheatsheet
Version: 3.12.3


## Contents

- [Operators](#Operators)
- [Built-in functions](#Built-in-functions)
- [Types](#Types)
- [Operations on sequences](#Operations-on-sequences)
- [Function definition](#Function-definition)
- [Class instantiation](#Class_instantiation)
- [Exceptions](#Exceptions)
- [Assert](#Assert)
- [Decorators](#Decorators)
- [Generator](#Generator)
- [File objects](#File-objects)
- [Iterator](#Iterator)
- [Annotations](#Annotations)
- [Context manager](#Context-manager)
- [Args & Kwargs](#Args-&-Kwargs)
- [Modules](#standard-modules)
- [Glossary](materials/python/md/glossary.md)
- [OOP](materials/python/md/7_oop.md)


## Operators

- __**__: Оператор возведения в степень;
- __%__: Остаток от деления;
- __//__: Оператор целочисленного деления;
- __/__: Оператор деления;
- __*__: Оператор умножения;
- __-__: Оператор вычитания;
- __+__: Оператор сложения;
- __=__: Оператор присваивания;
- __:=__: Оператор присваивания и возвращения значения переменной;
- __==__: Оператор сравнения равенства;
- __!=__: Оператор сравнения неравенства;
- __<__: Оператор меньше, чем;
- __>__: Оператор больше, чем;
- __<=__: Оператор меньше или равно;
- __>=__: Оператор больше или равно;
- __<<__: Побитовый сдвиг влево;
- __>>__: Побитовый сдвиг вправо;
- __&__: Побитовое и;
- __^__: Побитовое исключающее или;
- __|__: Побитовое или;
- __~__: Побитовое не;
- __not__: Булевый оператор не;
- __and__: Булевый оператор и;
- __is__: Проверка ID объектов;
- __in__: Наличие объекта в последовательности;
- __or__: Булевый оператор или;
- __if__: Оператор если;
- __else__: Оператор выполняется, если условие `if` и `elif` неверно;
- __elif__: Оператор выполняется, если условие `if` неверно;
- __... if ... else ...__: Тернарный условный оператор;
- [__switch-case__](materials/python/md/operators.md#switch-case): Оператор Switch-Case;
- __while__: Оператор повторного выполнения;
- __break__: Оператор выхода из цикла `while`;
- __continue__: Оператор возвращения к началу цикла;
- [__lambda__](materials/python/md/operators.md#lambda): Анонимная функция;
- [__for__](materials/python/md/operators.md#for): Оператор перебора итерируемых объектов.


## Built-in functions

- [__abs__](materials/python/md/built_in_functions.md/#abs): Возвращает абсолютное значение числа;
- [__aiter__](materials/python/md/built_in_functions.md/#aiter): Возвращает асинхронный итератор для асинхронной итерации;
- [__all__](materials/python/md/built_in_functions.md/#all): Имеют ли все элементы итерируемого объекта значение True;
- [__anext__](materials/python/md/built_in_functions.md/#anext): Возвращает следующий элемент из асинхронного итератора;
- [__any__](materials/python/md/built_in_functions.md/#any): Имеет ли какой-либо элемент итерируемого объекта значение True;
- [__ascii__](materials/python/md/built_in_functions.md/#ascii): Возвращает строку с печатным представлением объекта;
- [__bin__](materials/python/md/built_in_functions.md/#bin): Преобразует целое число в двоичную строку;
- [__bool__](materials/python/md/built_in_functions.md/#bool): Возвращает логическое значение;
- [__breakpoint__](materials/python/md/built_in_functions.md/#breakpoint): Помещает в отладчик на месте вызова;
- [__bytearray__](materials/python/md/built_in_functions.md/#bytearray): Возвращает изменяемый массив байт;
- [__bytes__](materials/python/md/built_in_functions.md/#bytes): Возвращает неименяемый массив байт;
- [__callable__](materials/python/md/built_in_functions.md/#callable): Является ли объект вызываемым;
- [__chr__](materials/python/md/built_in_functions.md/#chr): Возвращает символ по коду Юникода;
- [__classmethod__](materials/python/md/built_in_functions.md/#classmethod): Преоразует метод в метод класса;
- [__compile__](materials/python/md/built_in_functions.md/#compile): Компилирует строку в код, готовый к выполнению;
- [__complex__](materials/python/md/built_in_functions.md/#complex): Возвращает комплексное число;
- [__delattr__](materials/python/md/built_in_functions.md/#delattr): Удаляет именованный атрибут объекта;
- [__dict__](materials/python/md/built_in_functions.md/#dict): Возвращает словарь;
- [__dir__](materials/python/md/built_in_functions.md/#dir): Список имен в пространстве имен или атрибуты объекта;
- [__divmod__](materials/python/md/built_in_functions.md/#divmod): Возвращает частное и остаток введенных чисел;
- [__enumerate__](materials/python/md/built_in_functions.md/#enumerate): Возвращает перечисляемый объект;
- [__eval__](materials/python/md/built_in_functions.md/#eval): Вычисляет и выполняет введенное выражение;
- [__exec__](materials/python/md/built_in_functions.md/#exec): Выполняет Python код, полученный из строки;
- [__filter__](materials/python/md/built_in_functions.md/#filter): Фильтрует итерируемый объект заданной функцией;
- [__float__](materials/python/md/built_in_functions.md/#float): Возвращает число с плавающей запятой;
- [__format__](materials/python/md/built_in_functions.md/#format): Форматирует строку;
- [__frozenset__](materials/python/md/built_in_functions.md/#frozenset): Возвращает frozenset;
- [__getattr__](materials/python/md/built_in_functions.md/#getattr): Возвращает значение именованного атрибута объекта;
- [__globals__](materials/python/md/built_in_functions.md/#globals): Возвращает словарь глобальных переменных;
- [__hasattr__](materials/python/md/built_in_functions.md/#hasattr): Существует ли атрибут у объекта;
- [__hash__](materials/python/md/built_in_functions.md/#hash): Возвращает хеш объекта;
- [__help__](materials/python/md/built_in_functions.md/#help): Вызов встроенной справочной системы;
- [__hex__](materials/python/md/built_in_functions.md/#hex): Преобразует целое число в шестнадцатеричную строку;
- [__id__](materials/python/md/built_in_functions.md/#id): ID объекта;
- [__input__](materials/python/md/built_in_functions.md/#input): Принимает входные данные;
- [__int__](materials/python/md/built_in_functions.md/#int): Возвращает целочисленный объект;
- [__isinstance__](materials/python/md/built_in_functions.md/#isinstance): Является ли объект экземпляром другого объекта;
- [__issubclass__](materials/python/md/built_in_functions.md/#issubclass): Является ли класс является подклассом другого класса;
- [__iter__](materials/python/md/built_in_functions.md/#iter): Возвращает объект итератора;
- [__len__](materials/python/md/built_in_functions.md/#len): Возвращает длину объекта;
- [__list__](materials/python/md/built_in_functions.md/#list): Возвращает объект список;
- [__locals__](materials/python/md/built_in_functions.md/#locals): Возвращает словарь локальных переменных;
- [__map__](materials/python/md/built_in_functions.md/#map): Возвращает итератор, который применяет функцию к каждому итерируемому объекту;
- [__max__](materials/python/md/built_in_functions.md/#max): Возвращает наибольший элемент;
- [__memoryview__](materials/python/md/built_in_functions.md/#memoryview): Возвращает объект memoryview;
- [__min__](materials/python/md/built_in_functions.md/#min): Возвращает наименьший элемент;
- [__next__](materials/python/md/built_in_functions.md/#next): Получает следующий элемент итератора;
- [__object__](materials/python/md/built_in_functions.md/#object): Возвращает новый объект;
- [__oct__](materials/python/md/built_in_functions.md/#oct): Преобразует целое число в восьмеричную строку;
- [__open__](materials/python/md/built_in_functions.md/#open): Открывает файл и возвращает файловый объект;
- [__ord__](materials/python/md/built_in_functions.md/#ord): Возвращает код Юникода по символу;
- [__pow__](materials/python/md/built_in_functions.md/#pow): Возводит число в степень;
- [__print__](materials/python/md/built_in_functions.md/#print): Выводит объекты;
- [__property__](materials/python/md/built_in_functions.md/#property): Возвращает атрибут свойства;
- [__range__](materials/python/md/built_in_functions.md/#range): Возвращает неизменяемый тип последовательности range;
- [__repr__](materials/python/md/built_in_functions.md/#repr): Возвращает строку с представлением объекта;
- [__reversed__](materials/python/md/built_in_functions.md/#reversed): Возвращает обратный итератор;
- [__round__](materials/python/md/built_in_functions.md/#round): Округляет число;
- [__set__](materials/python/md/built_in_functions.md/#set): Возвращает объект set;
- [__setattr__](materials/python/md/built_in_functions.md/#setattr): Присваивает значение атрибуту;
- [__slice__](materials/python/md/built_in_functions.md/#slice): Возвращает часть объекта;
- [__sorted__](materials/python/md/built_in_functions.md/#sorted): Возвращает новый отсортированный список;
- [__staticmethod__](materials/python/md/built_in_functions.md/#staticmethod): Преобразует метод в статический;
- [__str__](materials/python/md/built_in_functions.md/#str): Возвращает объект str;
- [__sum__](materials/python/md/built_in_functions.md/#sum): Возвращает сумму элементов;
- [__super__](materials/python/md/built_in_functions.md/#super): Делегирует вызовы методов родительскому объекту;
- [__tuple__](materials/python/md/built_in_functions.md/#tuple): Возвращает неизменяемый тип последовательности tuple;
- [__type__](materials/python/md/built_in_functions.md/#type): Возвращает тип объекта;
- [__vars__](materials/python/md/built_in_functions.md/#vars): Возвращает object.\_\_dict__ иначе `locals`;
- [__zip__](materials/python/md/built_in_functions.md/#zip): Возвращает итератор кортежей;
- [__import__](materials/python/md/built_in_functions.md/#import): Вызывается оператором импорта.


## Types

- [__list__](materials/python/md/types.md#list): Изменяемая последовательность;
- [__tuple__](materials/python/md/types.md#tuple): Неизменяемая последовательность;
- [__dict__](materials/python/md/types.md#dict): Изменяемая последовательность пар ключ-значение;
- [__set__](materials/python/md/types.md#set): Изменяемая неупорядоченная коллекция уникальных хешируемых объектов;
- [__frozenset__](materials/python/md/types.md#frozenset): Изменяемая неупорядоченная коллекция уникальных хешируемых объектов;
- [__bytes__](materials/python/md/types.md#bytes): Неизменяемая последовательность байт;
- [__bytearray__](materials/python/md/types.md#bytearray): Изменяемая последовательность байт;
- [__memoryview__](materials/python/md/types.md#memoryview): Буфер памяти с данными;
- [__string__](materials/python/md/types.md#string): Неизменяемая последовательность;
- [__int__](materials/python/md/types.md#int): целые числа;
- [__float__](materials/python/md/types.md#float): Числа с плавающей точкой;
- [__complex__](materials/python/md/types.md#complex): Комплексные числа;
- [__bool__](materials/python/md/types.md#bool): Логические значения.


## Operations on sequences

- __x in s__: Элемент x находится в s;
- __x not in s__: Элемент x не находится в s;
- __s + t__: Объединение s и t;
- __s * n__: Добавление s к самому себе n раз;
- __s[i]__: i-й элемент из s;
- __s[i:j]__: последовательность от i до j из s;
- __s[i:j:k]__: последовательность от i до j с шагом k из s;
- __len(s)__: длина s;
- __min(s)__: наименьший элемент из s;
- __max(s)__: наибольший элемент из s;
- __s.index(x[, i[, j]])__: индекс первого вхождения x в s (в или после индекса i и перед индексом j);
- __s.count(x)__: количество x в s.


## Operations on mutable sequences

- __s[i] = x__: Элемент i из s заменяется на x;
- __s[i:j] = t__: Фрагмент s от i до j заменяется содержимым итерируемого t;
- __del s[i:j]__: Аналогично `s[i:j] = []`;
- __s[i:j:k] = t__: Элементы `s[i:j:k]` заменяются элементами t;
- __del s[i:j:k]__: Удаляет элементы `s[i:j:k];`
- __s.append(x)__: Добавляет x в конец последовательности;
- __s.clear()__: Удаляет все элементы из s;
- __s.copy()__: Создает неглубокую копию s (как `s[:]`);
- __s.extend(t)__: Расширяет s содержимым t;
- __s *= n__: Обновляет s, его содержимое повторяется n раз;
- __s.insert(i, x)__: Вставляет x в s перед индексом i;
- __s.pop()__: Извлекает элемент в i, а также удаляет его из s;
- __s.remove(x)__: Удаляет первый элемент из s, где `s[i] = x`;
- __s.reverse()__: Меняет местами элементы s на месте.


## Function definition

```python
def f(*args): pass  # f(1, 2, 3)
def f(x, *args): pass  # f(1, 2, 3)
def f(*args, z): pass  # f(1, 2, z=3)
def f(**kwds): pass  # f(x=1, y=2, z=3)
def f(x, **kwds): pass  # f(x=1, y=2, z=3) | f(1, y=2, z=3)
def f(*args, **kwds): pass  # f(x=1, y=2, z=3) | f(1, y=2, z=3) | f(1, 2, z=3) | f(1, 2, 3)
def f(x, *args, **kwds): pass  # f(x=1, y=2, z=3) | f(1, y=2, z=3) | f(1, 2, z=3) | f(1, 2, 3)
def f(*args, y, **kwds): pass  # f(x=1, y=2, z=3) | f(1, y=2, z=3)
def f(*, x, y, z): pass  # f(x=1, y=2, z=3)
def f(x, *, y, z): pass  # f(x=1, y=2, z=3) | f(1, y=2, z=3)
def f(x, y, *, z): pass  # f(x=1, y=2, z=3) | f(1, y=2, z=3) | f(1, 2, z=3)
```

Формат документирования функций:
```python
def foo():
    """
    Краткое описание функции с точкой на конце.

    Если есть подробности, то они указываются через пустую строку.
    """
    return

print(foo.__doc__)
```


## Class instantiation

```python
class A:
      """Строка документации класса."""
      def __init__(self, a):
            """Строка документации метода."""
            self.a = a
      def __repr__(self):
            """Используется repr(c), а также str(c) если __str__ не обьявлен."""
            return f'{self.__class__.__name__}({self.a!r})'
      def __str__(self):
            """Используется str(c), например print(c)"""
            return str(self.a)
      @classmethod
      def get_class_name(cls): # Принимает класс, а не экземпляр
            return cls.__name__
      @staticmethod
      def static(): # Ничего не принимает
            return 1

a = A(1)  # Создание экземпляра

# Создание экземпляра класса под капотом
obj = cls.__new__(cls, *args, **kwds)
if isinstance(obj, cls):
      obj.__init__(*args, **kwds)
```

```python
class B:
      @property
      def f(self):
            if not hasattr(self, '_f'): return
            return self._f
      @f.setter
      def f(self, value):
            self._f = value
```


## Exceptions

Обработка исключений позволяет избежать принудительного закрытия программы. Чтобы вызвать исключение принудительно, используется `raise`.
```python
try:
      # Код, который будет выполняться, пока не всплывет исключение.
      pass
except ValueError:
      # Код, который выполнится, если всплывет исключение ValueError.
      # В блоке try-except может быть указано неограниченное количество except блоков для перехвата ошибок.
      pass
except Exception:
      # Код, который выполнится, если будет вызвано любое исключение.
      pass
finally:
      # Код, который выполнится в любом случае
      pass
```

Пользовательские исключения создаются посредством создания класса, наследующего класс Exception.
```python
class CustomException(Exception):
      pass

raise CustomException
# Traceback (most recent call last):
#   File "<stdin>", line 1, in <module>
# __main__.CustomException
```


## Assert

`assert` - способ проверки утверждением.
```python
pod_bay_door_status = 'open'
assert pod_bay_door_status == 'open', 'The pod bay doors need to be "open".'

pod_bay_door_status = 'I\'m sorry, Dave. I\'m afraid I can\'t do that.'
assert pod_bay_door_status == 'open', 'The pod bay doors need to be "open".'
# Traceback (most recent call last):
#   File "<pyshell#10>", line 1, in <module>
#     assert pod_bay_door_status == 'open', 'The pod bay doors need to be "open".'
# AssertionError: The pod bay doors need to be "open".
```


## Decorators

Декоратор - это функция, которая принимает другую функцию в качестве аргумента и возвращает оболочку.
```python
def your_decorator(func):
      def wrapper():
            func()
      return wrapper

@your_decorator
def foo():
      print("Hello World!")

foo()
# Before func!
# Hello World!
# After func!
```

Декоратор для функции с параметрами:
```python
def your_decorator(func):
      def wrapper(*args,**kwargs):
            func(*args,**kwargs)
      return wrapper

@your_decorator
def foo(bar):
      print("My name is " + bar)

foo("Jack")
# Before func!
# My name is Jack
# After func!
```

Шаблон для создания декоратора для функций с параметрами и без них, с возвращаемым значением или без:
```python
import functools

def your_decorator(func):
      @functools.wraps(func)
      def wrapper(*args,**kwargs):
            result = func(*args,**kwargs)
            return result
      return wrapper


# Задание параметров декоратора
def your_decorator(arg):
      def decorator(func):
            @functools.wraps(func)
            def wrapper(*args,**kwargs):
                  result = func(*args,**kwargs)
                  return result
            return wrapper
      return decorator

@your_decorator(arg = 'x')
def foo(bar):
      return bar
```

Реализация декоратора на основе класса. Используется для сохранения состояния:
```python
class CountCallNumber:
      def __init__(self, func):
            self.func = func
            self.call_number = 0

      def __call__(self, *args, **kwargs):
            self.call_number += 1
            print("This is execution number " + str(self.call_number))
      return self.func(*args, **kwargs)

@CountCallNumber
def say_hi(name):
      print("Hi! My name is " + name)

say_hi("Jack")
# This is execution number 1
# Hi! My name is Jack

say_hi("James")
# This is execution number 2
# Hi! My name is James
```


## Generator

Функция-генератор - это функция или выражение, которое позволяет создавать итерируемые последовательности значений по мере их необходимости, вместо того, чтобы заранее создавать их все и хранить в памяти. Существует два основных типа генераторов: функциональные генераторы (с использованием ключевого слова yield) и генераторные выражения.
```python
def my_generator():
    yield 1
    yield 2

gen = my_generator()
print(next(gen)) # Вывод: 1


gen_expr = (x ** 2 for x in range(5))
print(list(gen_expr)) # Вывод: [0, 1, 4, 9, 16]
```
При каждом вызове next() генератор возобновляет работу с того места, где остановился (он запоминает все значения данных и то, какой оператор был выполнен последним). Методы __iter__ и __next__ создаются автоматически.
```python
def reverse(data):
    for i in range(len(data)-1, -1, -1):
        yield data[i]

for i in reverse('spam'):
    print(i)
```
Выражения-генераторы более компактны, но менее универсальны, чем полные определения генераторов, и более удобны для памяти, чем эквивалентные определения списков.
```python
sum(i*i for i in range(10))
```


## File objects

Режимы открытия файла: `r` - только для чтения, `w` - только для записи (очищает), `a` - для добавления, `r+` - для чтения и записи. Добавление `b` откроет в двоичном режиме. Рекомендуется использовать с конструкцией with, файл корректно закроется после завершения работы. Иначе необходимо вручную закрывать файл.
```python
f = open(filename, mode='r', encoding=None)
f.close()
# Или
with open('filepath', encoding="utf-8") as f:
    read_data = f.read()
```

Методы:
- __read__: Считывает некоторое количество данных и возвращает в виде строки или байтового обьекта.
- __readline__: Считывает одну строку из файла.
- __readlines__: Все строки файла в списке.
- __write__: Записывает строку в файл и возвращает кол-во записанных символов.
- __tell__: Возвращает целое число, указывающее текущую позицию файлового обьекта в файле, представленное как кол-во байтов от начала файла в двоичном режиме.
- __seek__: Изменение положения файлового обьекта.



## Iterator

Объект-итератор - обьект, представляющий поток данных. Итератор должен реализовать два метода: `__iter__()` - возвращает обьект итератора; `__next__()` - возвращает следующий элемент из итератора или `StopIteration`, если элементов нет.
```python
class SimpleIterator:
    def __init__(self, start, end):
        self.current = start
        self.end = end

    def __iter__(self):
        return self

    def __next__(self):
        if self.current < self.end:
            result = self.current
            self.current += 1
            return result
        else:
            raise StopIteration

my_iterator = SimpleIterator(1, 5)

for value in my_iterator:
    print(value)
```


## Annotations

Аннотации типов предназначены для предоставления информации о типах данных переменных, аргументов функций и возвращаемых значений. Аннотации типов являются необязательными и не влияют на фактическое выполнение кода во время выполнения программы. Основные встроенные типы аннотаций - `Generic Alias`, `Union`. `GenericAlias` чаще всего используется с классами-контейнерами (`list(int)`).
```python
def send_post_request(url: str, body: dict[str, int]) -> None: pass
```


## Context manager

Контекстный менеджер - объект, который реагирует, когда контекст начинается и заканчивается. Используется с `with`. Реализуется с помощью методов `__enter__()` и `__exit__()`, которые позволяют выполнить действия до выполнения кода и после. Например, файловые обьекты возвращают себя из `__enter__()`, чтобы их можно было открыть.
Кастомный контекстный менеджер:
```python
import contextlib
@contextlib.contextmanager
def context_manager(num):
      print('Enter')
      yield num + 1
      print('Exit')
with context_manager(2) as cm:
      print('Right in the middle with cm = {}'.format(cm))
# Enter
# Right in the middle with cm = 3
# Exit
```
Контекстный менеджер на основе класса:
```python
class ContextManager:
      def __enter__(self, *args, **kwargs):
            print("--enter--")

      def __exit__(self, *args):
            print("--exit--")

with ContextManager():
    print("test")
# --enter--
# test
# --exit--
```


## Args & Kwargs

`args` и `kwargs` - позволяют передавать функции неопределенное количество аргументов. Названия являются условными и могут быть изменены.
`args` позволяет передавать функции неограниченное количество безымянных аргументов.
```python
def foo(*args):
      print(f'Arguments passed: {args} as {type(args)}')

foo('arg1', 'arg2', 'arg3')  # Arguments passed: ('arg1', 'arg2', 'arg3') as <class 'tuple'>
```

`kwargs` позволяет передавать функции неограниченное количество именованных аргументов.
```python
def foo2(**kwargs):
      print(f'keywords: {kwargs} as {type(kwargs)}')

foo2(key1='arg1', key2='arg2')  # keywords: {'key1': 'arg1', 'key2': 'arg2'} as <class 'dict'>
```


## Modules

- Обработка текста
	- [__string__](materials/python/md/text_processing.md#string): Общие строковые операции;
	- [__re__](materials/python/md/text_processing.md#re): Операции с регулярными выражениями;
	- [__difflib__](materials/python/md/text_processing.md#difflib): Помощники для вычисления дельт;
	- [__textwrap__](materials/python/md/text_processing.md#textwrap): Перенос и заполнение текста;
	- [__unicodedata__](materials/python/md/text_processing.md#unicodedata): База данных Юникод;
	- [__stringprep__](materials/python/md/text_processing.md#stringprep): Подготовка интернет-строки;
	- [__readline__](materials/python/md/text_processing.md#readline): Интерфейс чтения GNU;
	- [__rlcompleter__](materials/python/md/text_processing.md#rlcompleter): Функция завершения для строки чтения GNU;
- Двоичные данные
	- [__struct__](materials/python/md/binary.md#struct): Интерпретирование байтов, как упакованные двоичные данные;
	- [__codecs__](materials/python/md/binary.md#codecs): Реестр кодеков и базовые классы;
- Типы данных
	- [__datetime__](materials/python/md/datatypes.md#datetime): Основные типы даты и времени;
	- [__zoneinfo__](materials/python/md/datatypes.md#zoneinfo): Поддержка часовых поясов IANA;
	- [__calendar__](materials/python/md/datatypes.md#calendar): Общие функции, связанные с календарем;
	- [__collections__](materials/python/md/datatypes.md#collections): Типы данных контейнера;
	- [__heapq__](materials/python/md/datatypes.md#heapq): Алгоритм очереди в куче;
	- [__bisect__](materials/python/md/datatypes.md#bisect): Алгоритм деления массива пополам;
	- [__array__](materials/python/md/datatypes.md#array): Эффективные массивы числовых значений;
	- [__weakref__](materials/python/md/datatypes.md#weakref): Слабые ссылки;
	- [__types__](materials/python/md/datatypes.md#types): Динамическое создание типов и имена для встроенных типов;
	- [__copy__](materials/python/md/datatypes.md#copy): Операции поверхностного и глубокого копирования;
	- [__pprint__](materials/python/md/datatypes.md#pprint): Симпатичный принтер данных;
	- [__reprlib__](materials/python/md/datatypes.md#reprlib): Альтернативная реализация repr();
	- [__enum__](materials/python/md/datatypes.md#enum): Поддержка перечислений;
	- [__graphlib__](materials/python/md/datatypes.md#graphlib): Функционал для работы с графоподобными структурами;
- Числа и математика
	- [__numbers__](materials/python/md/numeric.md#numbers): Числовые абстрактные базовые классы;
	- [__math__](materials/python/md/numeric.md#math): Математические функции;
	- [__cmath__](materials/python/md/numeric.md#cmath): Математические функции для комплексных чисел;
	- [__decimal__](materials/python/md/numeric.md#decimal): Десятичная арифметика с фиксированной запятой и с плавающей запятой;
	- [__fractions__](materials/python/md/numeric.md#fractions): Рациональное число;
	- [__random__](materials/python/md/numeric.md#random): Генерация псевдослучайных чисел;
	- [__statistics__](materials/python/md/numeric.md#statistics): Функции математической статистики;
- Функциональное программирование
	- [__itertools__](materials/python/md/functional.md#itertools): Функции, создающие итераторы для эффективного цикла;
	- [__functools__](materials/python/md/functional.md#functools): Функции высшего порядка и операции над вызываемыми объектами;
	- [__operator__](materials/python/md/functional.md#operator): Стандартные операторы как функции;
- Каталоги и файлы
	- [__pathlib__](materials/python/md/filesys.md#pathlib): Пути к объектно-ориентированной файловой системе;
	- [__fileinput__](materials/python/md/filesys.md#fileinput): Перебор строк из нескольких входных потоков;
	- [__stat__](materials/python/md/filesys.md#stat): Интерпретация stat() результатов;
	- [__filecmp__](materials/python/md/filesys.md#filecmp): Сравнение файлов и каталогов;
	- [__tempfile__](materials/python/md/filesys.md#tempfile): Генерировать временные файлы и каталоги;
	- [__glob__](materials/python/md/filesys.md#glob): Расширение шаблона пути в стиле Unix;
	- [__fnmatch__](materials/python/md/filesys.md#fnmatch): Сопоставление шаблонов имен файлов Unix;
	- [__linecache__](materials/python/md/filesys.md#linecache): Произвольный доступ к текстовым строкам;
	- [__shutil__](materials/python/md/filesys.md#shutil): Высокоуровневые файловые операции;
- Сохранение данных
	- [__pickle__](materials/python/md/persistence.md#pickle): Сериализация;
	- [__copyreg__](materials/python/md/persistence.md#copyreg): Регистрация pickle функций поддержки;
	- [__shelve__](materials/python/md/persistence.md#shelve): Сохранение объектов;
	- [__marshal__](materials/python/md/persistence.md#marshal): Внутренняя сериализация объектов;
	- [__dbm__](materials/python/md/persistence.md#dbm): Интерфейсы к «базам данных» Unix;
	- [__sqlite3__](materials/python/md/persistence.md#sqlite3): Интерфейс DB-API 2.0 для SQLite;
- Сжатие и архивирование данных
	- [__zlib__](materials/python/md/archiving.md#zlib): Сжатие, совместимое с gzip;
	- [__gzip__](materials/python/md/archiving.md#gzip): Поддержка файлов gzip;
	- [__bz2__](materials/python/md/archiving.md#bz2): Поддержка сжатия bzip2;
	- [__lzma__](materials/python/md/archiving.md#lzma): Сжатие с использованием алгоритма LZMA;
	- [__zipfile__](materials/python/md/archiving.md#zipfile): Работа с ZIP-архивами;
	- [__tarfile__](materials/python/md/archiving.md#tarfile): Чтение и запись файлов tar-архива;
- Форматы файлов
	- [__csv__](materials/python/md/archiving.md#csv): Чтение и запись CSV-файлов;
	- [__configparser__](materials/python/md/archiving.md#configparser): Парсер конфигурационного файла;
	- [__tomllib__](materials/python/md/archiving.md#tomllib): Разбор файлов TOML;
	- [__netrc__](materials/python/md/archiving.md#netrc): обработка файлов netrc;
	- [__plistlib__](materials/python/md/archiving.md#plistlib): Генерация и анализ .plist файлов Apple;
- Криптография
	- [__hashlib__](materials/python/md/crypto.md#hashlib): Безопасные хэши и дайджесты сообщений;
	- [__hmac__](materials/python/md/crypto.md#hmac): Хеширование ключей для аутентификации сообщений;
	- [__secrets__](materials/python/md/crypto.md#secrets): Генерация безопасных случайных чисел для управления секретами;
- Службы ОС
	- [__os__](materials/python/md/allos.md#os): Различные интерфейсы операционной системы;
	- [__io__](materials/python/md/allos.md#io): Основные инструменты для работы с потоками;
	- [__time__](materials/python/md/allos.md#time): Доступ ко времени и конверсии;
	- [__argparse__](materials/python/md/allos.md#argparse): Анализатор параметров командной строки, аргументов и подкоманд;
	- [__getopt__](materials/python/md/allos.md#): Синтаксический анализатор в стиле C для параметров командной строки;
	- [__logging__](materials/python/md/allos.md#logging): Логирование;
	- [__getpass__](materials/python/md/allos.md#getpass): Портативный ввод пароля;
	- [__curses__](materials/python/md/allos.md#curses): Обработка терминала для отображения символьных ячеек;
	- [__platform__](materials/python/md/allos.md#platform): Доступ к идентификационным данным базовой платформы;
	- [__errno__](materials/python/md/allos.md#errno): Стандартные системные символы ошибок;
	- [__ctypes__](materials/python/md/allos.md#ctypes): Сторонние функции;
- Параллельное выполнение
	- [__threading__](materials/python/md/concurrency.md#threading): Потоковый параллелизм;
	- [__multiprocessing__](materials/python/md/concurrency.md#multiprocessing): Параллелизм на основе процессов;
	- [__concurrent__](materials/python/md/concurrency.md#concurrent): запуск параллельных задач;
	- [__subprocess__](materials/python/md/concurrency.md#subprocess): Управление подпроцессами;
	- [__sched__](materials/python/md/concurrency.md#sched): Планировщик событий;
	- [__queue__](materials/python/md/concurrency.md#queue): Класс синхронизированной очереди;
	- [__contextvars__](materials/python/md/concurrency.md#contextvars): Контекстные переменные;
	- [__thread__](materials/python/md/concurrency.md#): Низкоуровневый API многопоточности;
- Сети и межпроцессное взаимодействие
	- [__asyncio__](materials/python/md/ipc.md#asyncio): Асинхронный ввод-вывод;
	- [__socket__](materials/python/md/ipc.md#socket): Низкоуровневый сетевой интерфейс;
	- [__ssl__](materials/python/md/ipc.md#ssl): Оболочка TLS/SSL для объектов сокетов;
	- [__select__](materials/python/md/ipc.md#select): Ожидание завершения ввода-вывода;
	- [__selectors__](materials/python/md/ipc.md#selectors): Высокоуровневое мультиплексирование ввода-вывода;
	- [__signal__](materials/python/md/ipc.md#signal): Установите обработчики асинхронных событий;
	- [__mmap__](materials/python/md/ipc.md#mmap): Поддержка файлов, отображаемых в памяти;
- Обработка данных через Интернет
	- [__email__](materials/python/md/netdata.md#email): Пакет обработки электронной почты и MIME;
	- [__json__](materials/python/md/netdata.md#json): Кодер и декодер JSON;
	- [__mailbox__](materials/python/md/netdata.md#mailbox): Работа с почтовыми ящиками различных форматов;
	- [__mimetypes__](materials/python/md/netdata.md#mimetypes): Сопоставление имен файлов с типами MIME;
	- [__base64__](materials/python/md/netdata.md#base64): Кодировки данных Base16, Base32, Base64, Base85;
	- [__binascii__](materials/python/md/netdata.md#binascii): Преобразование между двоичным кодом и ASCII;
	- [__quopri__](materials/python/md/netdata.md#quopri): Кодирование и декодирование MIME-данных, пригодных для печати;
- Обработка структурированной разметки
	- [__html__](materials/python/md/markup.md#html): Поддержка языка гипертекстовой разметки;
	- [__xml__](materials/python/md/markup.md#xml): Модули обработки XML;
- Интернет протоколы и поддержка
	- [__webbrowser__](materials/python/md/internet.md#webbrowser): Контроллер веб-браузера;
	- [__wsgiref__](materials/python/md/internet.md#wsgiref): Утилиты WSGI и эталонная реализация;
	- [__urllib__](materials/python/md/internet.md#urllib): Модули обработки URL-адресов;
	- [__http__](materials/python/md/internet.md#http): HTTP-модули;
	- [__ftplib__](materials/python/md/internet.md#ftplib): Клиент FTP-протокола;
	- [__poplib__](materials/python/md/internet.md#poplib): Клиент протокола POP3;
	- [__imaplib__](materials/python/md/internet.md#imaplib): Клиент протокола IMAP4;
	- [__smtplib__](materials/python/md/internet.md#smtplib): Клиент протокола SMTP;
	- [__uuid__](materials/python/md/internet.md#uuid): Объекты UUID;
	- [__socketserver__](materials/python/md/internet.md#socketserver): Фреймворк для сетевых серверов;
	- [__xmlrpc__](materials/python/md/internet.md#xmlrpc): Серверные и клиентские модули XMLRPC;
	- [__ipaddress__](materials/python/md/internet.md#ipaddress): Библиотека манипуляций IPv4/IPv6;
- Мультимедиа
	- [__wave__](materials/python/md/mm.md#wave): Чтение и запись файлов WAV;
	- [__colorsys__](materials/python/md/mm.md#colorsys): Преобразования между цветовыми системами;
- Интернализация
	- [__gettext__](materials/python/md/i18n.md#gettext): Услуги многоязычной интернационализации;
	- [__locale__](materials/python/md/i18n.md#locale): Услуги по интернационализации;
- Рамки программы
	- [__turtle__](materials/python/md/frameworks.md#turtle): Графика черепахи;
	- [__cmd__](materials/python/md/frameworks.md#cmd): Поддержка интерпретаторов строко-ориентированных команд;
	- [__shlex__](materials/python/md/frameworks.md#shlex): Простой лексический анализ;
- Графические инструменты
	- [__tkinter__](materials/python/md/tk.md#tkinter): Интерфейс Python для Tcl/Tk;
- Инструменты разработки
	- [__typing__](materials/python/md/development.md#typing): Аннотации типов;
	- [__pydoc__](materials/python/md/development.md#pydoc): Генератор документации и онлайн-справочная система;
	- [__doctest__](materials/python/md/development.md#doctest): Тест интерактивных примеров;
	- [__2to3__](materials/python/md/development.md#2to3): Автоматический перевод кода Python 2 на 3;
	- [__unittest__](materials/python/md/development.md#unittest): Фреймворк модульного тестирования;
	- [__test__](materials/python/md/development.md#test): Пакет регрессионных тестов для Python;
- Отладка и профилирование
	- [__bdb__](materials/python/md/debug.md#bdb): Фреймворк отладчика;
	- [__faulthandler__](materials/python/md/debug.md#faulthandler): Дамп обратной трассировки Python;
	- [__pdb__](materials/python/md/debug.md#pdb): Отладчик Python;
	- [__timeit__](materials/python/md/debug.md#timeit): Измерение времени выполнения небольших фрагментов кода;
	- [__trace__](materials/python/md/debug.md#trace): Отслеживание или отслеживание выполнения операторов Python;
	- [__tracemalloc__](materials/python/md/debug.md#tracemalloc): Отслеживание распределения памяти;
- Упаковка и распространение ПО
	- [__ensurepip__](materials/python/md/distribution.md#ensurepip): Загрузка pip установщика;
	- [__venv__](materials/python/md/distribution.md#venv): Создание виртуальных сред;
	- [__zipapp__](materials/python/md/distribution.md#zipapp): Управление исполняемыми zip-архивами Python;
- Время выполнения Python
	- [__sys__](materials/python/md/python.md#sys): Параметры и функции, специфичные для системы;
	- [__sysconfig__](materials/python/md/python.md#sysconfig): Информация о конфигурации Python;
	- [__builtins__](materials/python/md/python.md#builtins): Встроенные объекты;
	- [__\_\_main__\_\_](materials/python/md/python.md#main): Среда кода верхнего уровня;
	- [__warnings__](materials/python/md/python.md#warnings): Контроль предупреждения;
	- [__dataclasses__](materials/python/md/python.md#dataclasses): Классы данных;
	- [__contextlib__](materials/python/md/python.md#contextlib): Утилиты для with контекстов операторов;
	- [__abc__](materials/python/md/python.md#abc): Абстрактные базовые классы;
	- [__atexit__](materials/python/md/python.md#atexit): Обработчики выхода;
	- [__traceback__](materials/python/md/python.md#traceback): Распечатать или получить обратную трассировку стека;
	- [__\_\_future__\_\_](materials/python/md/python.md#future): Определения будущих операторов;
	- [__gc__](materials/python/md/python.md#gc): Интерфейс сборщика мусора;
	- [__inspect__](materials/python/md/python.md#inspect): Осмотр живых объектов;
	- [__site__](materials/python/md/python.md#site): Перехватчик конфигурации для конкретного сайта;
- Пользовательские интерпретаторы
	- [__code__](materials/python/md/custominterp.md#code): Базовые классы интерпретатора;
	- [__codeop__](materials/python/md/custominterp.md#codeop): Скомпилировать код Python;
- Импорт модулей
	- [__zipimport__](materials/python/md/modules.md#zipimport): Импорт модулей из Zip-архивов;
	- [__pkgutil__](materials/python/md/modules.md#pkgutil): Утилита расширения пакета;
	- [__modulefinder__](materials/python/md/modules.md#modulefinder): Найти модули, используемые скриптом;
	- [__runpy__](materials/python/md/modules.md#runpy): Поиск и выполнение модулей Python;
	- [__importlib__](materials/python/md/modules.md#importlib): Реализация import;
- Языковые службы
	- [__ast__](materials/python/md/language.md#ast): Абстрактные синтаксические деревья;
	- [__symtable__](materials/python/md/language.md#symtable): Доступ к таблицам символов компилятора;
	- [__token__](materials/python/md/language.md#token): Константы, используемые с деревьями синтаксического анализа Python;
	- [__keyword__](materials/python/md/language.md#keyword): Тестирование ключевых слов Python;
	- [__tokenize__](materials/python/md/language.md#tokenize): Токенизатор для исходного кода Python;
	- [__tabnanny__](materials/python/md/language.md#tabnanny): Обнаружение неоднозначного отступа;
	- [__pyclbr__](materials/python/md/language.md#pyclbr): Поддержка браузера модуля Python;
	- [__py_compile__](materials/python/md/language.md#py_compile): Скомпилируйте исходные файлы Python;
	- [__compileall__](materials/python/md/language.md#compileall): Байт-компилируемые библиотеки Python;
	- [__dis__](materials/python/md/language.md#dis): Дизассемблер для байт-кода Python;
	- [__pickletools__](materials/python/md/language.md#pickletools): Инструменты для разработчиков Pickle;
- Сторонние модули
	- [__Numpy__](materials/python/md/third_party_modules.md#numpy): Математические операции с многомерными массивами и матрицами;
	- [__Pandas__](materials/python/md/third_party_modules.md#pandas): Анализ и обработка данных, машинное обучение, статистика;
	- [__Matplotlib__](materials/python/md/third_party_modules.md#matplotlib): Визуализация данных в виде графиков и диаграмм;
	- [__SciPy__](materials/python/md/third_party_modules.md#scipy): Научные и инженерные расчеты (оптимизация, интегрирование, интерполяция, обработка сигналов, статистика);
	- [__TensorFlow__](materials/python/md/third_party_modules.md#tensorflow): Создание, обучение, развертывание моделей машинного обучения;
	- [__Keras__](materials/python/md/third_party_modules.md#keras): Удобный способ создания моделей машинного обучения;
	- [__Scikit-learn__](materials/python/md/third_party_modules.md#scikit-learn): Простые и эффективные инструменты для анализа данных и построения моделей;
	- [__XGBoost__](materials/python/md/third_party_modules.md#XGBoost): Библиотека машинного обучения, основанная на методе градиентного бустинга;
	- [__LightGBM__](materials/python/md/third_party_modules.md#LightGBM): Библиотека градиентного бустинга, разработанная для эффективного и быстрого обучения моделей на больших объемах данных;
	- [__requests__](materials/python/md/third_party_modules.md#requests): Работа с HTTP протоколом;
	- [__Beautiful Soup__](materials/python/md/third_party_modules.md#Beautiful-Soup): Парсинг HTML и XML документов;
	- [__lxml__](materials/python/md/third_party_modules.md#lxml): Обработка XML и HTML;
	- [__SQLite3__](materials/python/md/third_party_modules.md#sqlite3): Работа с СУБД SQLite;
	- [__mysql__](materials/python/md/third_party_modules.md#mysql): Работа с СУБД MySQL;
	- [__psycopg2__](materials/python/md/third_party_modules.md#psycopg2): Работа с СУБД PostgreSQL;
	- [__Seaborn__](materials/python/md/third_party_modules.md#seaborn): Визуализация данных;
	- [__Plotly__](materials/python/md/third_party_modules.md#plotly): Создание интерактивных графиков для веб-приложений;
	- [__Bokeh__](materials/python/md/third_party_modules.md#bokeh): Создание интерактивных графиков для веб-приложений;
	- [__OpenCV__](materials/python/md/third_party_modules.md#opencv): Компьютерное зрение и обработка изображений;
	- [__Pillow__](materials/python/md/third_party_modules.md#pillow): Работа с изображениями;