# Функциональное программирование
1. [itertools - Функции, создающие итераторы для эффективного цикла](#1)
2. [functools - Функции высшего порядка и операции над вызываемыми объектами](#2)
3. [operator - Стандартные операторы как функции](#3)


## <div id="1">1. itertools - Функции, создающие итераторы для эффективного цикла</div>
> Модуль стандартизирует набор быстрых инструментов с эффективным использованием памяти для работы с итерируемыми обьектами.
### Использование
- Обьединение нескольких итерируемых обьектов.
- Создание бесконечного циклического итератора из заданного итерируемого обьекта.
- Создание бесконечного итератора арифметической прогрессии.
- Получение декартово произведение заданных итерируемых обьектов.
### Примеры
```python
colors = ['red', 'green', 'blue']
for color in itertools.cycle(colors):
    print(color)

numbers = [-1, 2, -3, 4, -5]
positive_numbers = itertools.filterfalse(lambda x: x < 0, numbers)
print(list(positive_numbers)) # [2, 4]

items = ['A', 'B', 'C']
for combo in itertools.combinations(items, 2):
    print(combo)

list1 = [1, 2, 3]
list2 = ['a', 'b', 'c']
result = list(itertools.chain(list1, list2))
print(result) # [1, 2, 3, 'a', 'b', 'c']

for i in itertools.count(start=1, step=2):
    print(i)
```


## <div id="2">2. functools - Функции высшего порядка и операции над вызываемыми объектами</div>
> Модуль предназначен для функций высшего порядка: функций, которые воздействуют на другие функции или возвращают их.
### Использование
- Написание декораторов.
- Кеширование результатов вызова функции с ограниченным размером кэша для ускорения функций.
### Примеры
```python
@cache
def factorial(n):
    return n * factorial(n-1) if n else 1

factorial(10) # 362880011
factorial(5) # Выведет кэшированный результат -> 120
factorial(12) # Выполнит два рекурсивных вызова -> 479001600
```

## <div id="3">3. operator - Стандартные операторы как функции</div>
> Модуль предоставляет функции, которые соответствуют встроенным операторам языка.
### Использование
- Упрощение кода, повышение читабельности.
### Примеры
```python
result = operator.floordiv(10, 3)  # 3
result = operator.eq(5, 10) # False
result = operator.ne(5, 10) # True


class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

people = [Person('Alice', 25), Person('Bob', 30)]
names = list(map(operator.attrgetter('name'), people))
```