# ООП

## Статические методы
Статические методы - методы, которые принадлежат классу, а не экземпляру класса. Они не могут изменять состояние обьекта, так как не имеют доступа к его состоянию. Вместо этого они работают с аргументами, которые передаются в них соответственно.
Статические методы обьявляются с помощью декоратора `@staticmethod`. Статические методы не принимают первым аргументом self.
### Примеры
Допустим, есть класс, который выполняет различные математические операции. Добавим метод, который будет вычислять квадрат числа, но он не будет использовать или изменять состояние экземпляра класса:
```python
class Calculator:
    @staticmethod
    def square(n):
        return n * n
```


## Методы класса
Методы класса - методы, которые не привязаны к классу, а не к экземпляру. Они могут изменять состояние класса, но не могут изменять состояние обьекта класса. Методы класса обьявляются с помощью декоратора `@classmethod`. Методы класса принимают ссылку на класс, которые вызывают метод, в качестве первого аргумента (обычно это cls). Преимущество в том, что они могут быть наследованы и переопределены в подклассах.
### Примеры
```python
class Calculator:
    a = 10

    @classmethod
    def print_a(cls):
        print(cls.a)
```


## Магические методы и атрибуты
Название | Описание
--- | ---
`__name__` | Имя класса
`__module__` | Имя модуля, в котором определен класс
`__dict__` | Словарь, содержащий пространство имен класса
`__bases__` | Кортеж, содержащий базовые классы в порядке их появления в списке базовых классов
`__doc__` | Строка документации класса или None
`__annotations__` | Словарь, содержащий аннотации переменных, собранные во время выполнения тела класса
`__type_params__` | Кортеж, содержащий параметры типа универсального класса
`__new__(cls, ...)` | 
`__init__(self, ...)` |
`__del__(self)` | 
`__repr__(self)` |
`__str__(self)` |
`__bytes__(self)` |
`__format__(self, format_spec)` |
`__lt__(self, other)`, `__le__(self, other)`, `__eq__(self, other)`, `__ne__(self, other)`, `__gt__(self, other)`, `__ge__(self, other)` | 
`__hash__(self)` | 
`__bool__(self)` | 
`__getattr__(self, name)` | 
`__getattribute__(self, name)` | 
`__setattr__(self, name, value)` | 
`__delattr__(self, name)` | 
`__dir__(self)` | 
`__get__(self, instance, owner=None)` | 
`__set__(self, instance, value)` | 
`__delete__(self, instance)` | 
`__objclass__` | 
`__slots__` | 
`__init_subclass__(cls)` | 
`__set_name__(self, owner, name)` | 
`__mro_entries__(self, bases)` | 
`__instancecheck__(self, instance)` | 
`__instancecheck__(self, instance)` | 
`__class_getitem__(cls, key)` | 
`__call__(self, ...)` | 
`__len__(self)` | 
`__length_hint__(self)` | 
`__getitem__(self, key)` | 
`__setitem__(self, key, value)` | 
`__delitem__(self, key)` | 
`__missing__(self, key)` | 
`__iter__(self)` | 
`__reversed__(self)` | 
`__contains__(self, item)` | 
`object.__add__(self, other)`, `__sub__(self, other)`, `__mul__(self, other)`, `__matmul__(self, other)`, `__truediv__(self, other)`, `__floordiv__(self, other)`, `__mod__(self, other)`, `__divmod__(self, other)`, `__pow__(self, ...)`, `__lshift__(self, other)`, `__rshift__(self, other)`, `__and__(self, other)`, `__xor__(self, other)`, `__or__(self, other)` | 
`__radd__(self, other)`, `__rsub__(self, other)`, `__rmul__(self, other)`, `__rmatmul__(self, other)`, `__rtruediv__(self, other)`, `__rfloordiv__(self, other)`, `__rmod__(self, other)`, `__rdivmod__(self, other)`, `__rpow__(self, ...)`, `__rlshift__(self, other)`, `__rrshift__(self, other)`, `__rand__(self, other)`, `__rxor__(self, other)`, `__ror__(self, other)` | 
`__iadd__(self, other)`, `__isub__(self, other)`, `__imul__(self, other)`, `__imatmul__(self, other)`, `__itruediv__(self, other)`, `__ifloordiv__(self, other)`, `__imod__(self, other)`, `__ipow__(self, ...)`, `__ilshift__(self, other)`, `__irshift__(self, other)`, `__iand__(self, other)`, `__ixor__(self, other)`, `__ior__(self, other)` | 
`__neg__(self)`, `__pos__(self)`, `__abs__(self)`, `__invert__(self)` | 
`__complex__(self)`, `__int__(self)`, `__float__(self)` | 
`__index__(self)` | 
`__round__(self, ...)`, `__trunc__(self)`, `__floor__(self)`, `__ceil__(self)` | 
`__enter__(self)` | 
`__exit__(self, exc_type, exc_value, traceback)` | 
`__match_args__` | 
`__buffer__(self, flags)` | 
`__release_buffer__(self, buffer)` | 
`__await__(self)` | 
`__aiter__(self)` | 
`__anext__(self)` | 
`__aenter__(self)` | 
`__aexit__(self, exc_type, exc_value, traceback)` | 


Пример атрибутов класса:
```python
class MyClass(object):
    pass


print(MyClass.__name__) # MyClass
print(MyClass.__module__) # __main__
print(MyClass.__dict__) # {'__module__': '__main__', '__dict__': <attribute '__dict__' of 'MyClass' objects>, '__weakref__': <attribute '__weakref__' of 'MyClass' objects>, '__doc__': None}
print(MyClass.__bases__) # (<class 'object'>,)
print(MyClass.__doc__) # None
print(MyClass.__annotations__) # {}
```