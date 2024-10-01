# SQLModel

- Version: 0.0.22
- https://sqlmodel.tiangolo.com
- https://github.com/fastapi/sqlmodel

```
pip install sqlmodel
```
- Установка модуля

## Создание таблицы

```python
from sqlmodel import Field, SQLModel, create_engine


class Product(SQLModel, table=True):
	id: int | None = Field(default=None, primary_key=True)
	title: str
	description: str | None = Field(default=None)


sqlite_file_name = "database.db"
sqlite_url = f"sqlite:///{sqlite_file_name}"
engine = create_engine(sqlite_url, echo=True)

def create_db_and_tables():
	SQLModel.metadata.create_all(engine)

create_db_and_tables()
```
- Класс Product представляет таблицу БД. Каждый будущий экземпляр этого класса будет представлять строку в таблице
- table=True сообщает SQLModel, что это таблица. Без этой записи это будут только модели
- Имена переменных в классе будут именами столбцов в таблице. Типы каждого из них будут типами столбца
- Поле id является первичным ключом таблицы, так как мы установили аргумент primary_key=True. Это значение будет сгенерировано БД, а не ORM. Это значение будет None до тех пор, пока экзмепляр не будет сохранен в БД
- create_engine - обьект, который управляет связью с БД. Например он может хранить сетевые подключения к PostgreSQL. echo=True выводит сообщения об операциях для отладки
- SQLModel.metadata.create_all(engine) создает БД и таблицы (Классы SQLModel с аргументом table=True)

## Создание строк

Код в предыдущем примере используется здесь, но не отображается.

```python
from sqlmodel import Session


def create_products():
	p1 = Product(title="Boots")
	p2 = Product(title="T-shirt")

	with Session(engine) as session:
		session.add(p1)
		session.add(p2)
		session.commit()
		session.refresh(p1)
		session.refresh(p2)

	# Или

	session = Session(engine)
	...
	session.close()
```
- Session(engine) создает новую сессию для каждой группы операций с БД
- Добавляем экземпляры модели Product в сессию
- Сеанс хранит в памяти все обьекты, которые будут сохранены в БД, когда будет вызван коммит (session.commit())
- Сеанас создает новую транзакцию и выполняет весь SQL код в этой транзакции
- session.refresh() обращается к движку для получения  данных для указанного обьекта из БД, и сохраняет эти данных в этот обьект

## Чтение данных

```python
from sqlmodel import select


def select_products():
	with Session(engine) as session:
		st = select(Product)
		results = session.exec(st)
		for product in results:
			print(product)
		# или
		print(results.all())
```
- Функция создает новую сессию, вызывает функцию select, которой передает модель Product. Это означает, что нам нужно получить все столбцы этой модели. Возвращаемое значение представляет специальный обьект
- session.exec() выполнит SQL запрос и вернет результат в виде таблицы, который с помощью цикла выводим
- results.all() выводит список результатов

### Чтение одной строки

```python
statement = select(Product)
results = session.exec(statement)
product = results.first()

product = results.one()

product = session.get(Product, 1)
```
- Получение первой строки ответа. first - это метод объекта результатов, а не оператора select. Если результата нет, то выведет None
- one отличается тем, что если результат больше одного, то вернет ошибку
- get позволяет получить одну запись (Аналогично first())

### Чтение диапазона данных

```python
statement = select(Product).limit(3).offset(3)
```
- limit указывает на кол-во возвращаемых записей
- offset указывает пропустить определенное кол-во записей перед возвратом

## Фильтрация данных

```python
from sqlmodel import or_, col


statement = select(Product).where(Product.title == "a", ...).where(...)

statement = select(Product).where(or_(..., ...))

statement = select(Product).where(col(Product.price) > 1000)
```
- Добавляем where к оператору select и фильтруем вывод по фильтру. Можно добавлять несколько where или указывать несколько условий в одном where через запятую. Для условия используется оператор равенства, а не символ равно. Однако такое условие возвращает обьект модели, а не булево значение. Это работает только для атрибутов класса модели
- Возвращаемый обьект это всегда итерируемый обьект, даже если возвращена только одна строка
- Все операторы сравнения: `<`, `>`, `==`, `>=`, `<=`, `!=`
- Чтобы создавать условия ИЛИ, можно использовать or_
- col указывает редактору, что это специальный столбец SQLModel

## Индексация

```python
class Product(SQLModel, table=True):
	id: int | None = Field(default=None, primary_key=True)
	title: str = Field(index=True)
	price: int = Field(index=True)
	description: str | None = None
```
- Индексация позволяет быстрее находить нужные данные. В данном случае индексируется два поля, так как они будут использоваться для фильтрации товаров. Поле описание товара не индексируется, чтобы не занимать лишнюю память и вычислительные ресурсы. Для поля ID создается внутренний индекс автоматически БД

## Обновление данных

```python
statement = select(Product).where(Product.id == 1)
results = session.exec(statement)
product = results.one()
product.price = 1000
session.add(product)
session.commit()
session.refresh(product)
```
- Получаем запись из БД, меняем значение поля price. Сохраняем обьект из памяти в сессию БД
- refresh обновляет обьект в памяти с помощью отправки sql запроса в БД для получения данных обьекта

## Удаление данных

```python
statement = select(Product).where(Product.id == 1)
resulsts = session.exec(statement)
product = results.one()
session.delete(Product)
session.commit()
```
- Получаем обьект из БД, удаляем запись из сеанса и коммитим. В итоге один запрос к БД. Обьект также доступен в памяти, но его больше нет в БД

## Связанные таблицы

```python
class Product(SQLModel, table=True):
	...
	category_id: int = Field(foreign_key="category.id")
```
- Создание поля, которое является связью many-to-one. foreign_key означает, что этот столбец будет иметь ключ для идентификации строки во внешней таблице

```python
cat1 = Category(title=...)
session.add(cat1)
session.commit()

pr1 = Product(title=..., category_id=cat1.id)
session.add(pr1)
session.commit()
session.refresh(pr1)
```
- Создание записи в таблицу категорий, а затем создание товара, который имеет зависимость от этой категории

### Получение данных

```python
statement = select(Product, Category).where(Product.category_id == Category.id)
```
- Выполняем select с двумя моделями и получаем кортежи экземпляров этих двух моделей

```sql
SELECT product.id, product.name, category.name
FROM product, category
WHERE product.team_id = category.id

-- Или

SELECT product.id, product.name, category.name
FROM product
JOIN category
ON product.team_id = category.id
```
- Запись выше соответствует одной из этих записей

```python
statement = select(Product, Category).join(Category)
```
- Чтобы выполнить второй SQL запрос из записи выше, можно использовать такой синтаксис

```python
statement = select(Product).join(Category)
```
- Такая запись отличается тем, что возвращает один кортеж с обьектами Product, а обьекты Category не будут возвращены

#### LEFT JOIN

```python
statement = Product(Product, Category).join(Category, isouter=True)
```
- Такое выражение вернет даже те записи из таблицы Product, которые не имеют связи с таблицей Category

#### UPDATE CONNECTION

```python
...
product.category_id = cat2.id
session.add(product)
session.commit()
session.refresh(product)
```
- Изменение связи товара к категории

#### DELETE CONNECTION

```python
...
product.category_id = None
session.add(product)
session.commit()
session.refresh(product)
```
- Удаление связи между строками в таблицах

## Relationship

```python
from sqlmodel import relationship, SQLModel, Field


class Category(SQLModel, table=True):
	id: int | None = Field(default=None, primary_key=True)
	title: str

	products: list["Product"] = Relationship(back_populates="category")


class Product(SQLModel, table=True):
	id: int | None = Field(default=None, primary_key=True)
	title: str

	category_id: int = Field(foreign_key="category.id")
	category: Category = Relationship(back_populates="products")
```
- Атрибуты category, products не представляют столбец в БД, их значение представляет весь связанный обьект. Если обратиться к Product.category, то будет получен весь обьект экземпляра Category, которому принадлежит этот товар
```python
product.category.title
```
- Вывод названия категории к которой принадлежит товар
```python
cat1 = Category(title=...)
cat2 = Category(title=...)

pr1 = Product(title=..., category=cat1)
pr2 = Product(title=..., category=cat2)

session.add(pr1)
session.add(pr2)
session.commit()
session.refresh(pr1)
session.refresh(pr2)
```
- Создание моделей с помощью relationship. Благодаря зависимостям мы можем создать обьекты категорий и БЕЗ коммита создать обьекты товаров, которые будут иметь связь с этими категориями. Это позволяет не использовать идентификатор
- После создания товаров, категории автоматически добавятся в БД, а также полям category_id будут присвоены соответствующие id
```python
pr1 = Product(title=...)
pr2 = Product(title=...)

cat1 = Category(
	title=...,
	products=[pr1, pr2]
)
session.add(cat1)
session.commit()
session.refresh(cat1)
```
- Точно также можно создавать обьекты категорий. В примере мы создаем два обьекта товаров, затем создаем категорию и присваиваем ей эти два товара. Это позволяет нам закомитить всего один раз. Все остальное создастся автоматически
```python
pr3 = Product(title=...)

cat1.products.append(pr3)
session.add(cat1)
session.commit()
session.refresh(pr3)
```
- Также можно добавлять зависимости

### Read

```python
statement = select(Product).where(Product.id == 1)
result = session.exec(statement)
product = result.one()

statement = select(Category).where(Category.id = product.category_id)
result = session.exec(statement)
category = result.first()
```
- Получаем товар, с помощью ид категории этого товара получаем категорию. Это решение, если бы не было атрибутов отношений
```python
statement = select(Product).where(Product.id = 1)
result = session.exec(statement)
product = result.one()
category = product.category
```
- Получение обьекта категории, используя атрибут отношений

### Remove

```python
statement = select(Product).where(Product.id == 1)
result = session.exec(statement)
product = result.one()

product.category = None
session.add(product)
session.commit()
session.refresh(product)
```
- Удаление связи, устанавливая значение None

### back_populates

Значением этого атрибута является строка с именем атрибута в другом клссе модели. Этот атрибут сообщает о том, что если что-то изменится в этой модели, то изменится этот атрибут в другой модели. Это работает даже до фиксации сеанса.

### CASCASE DELETE RELATIONSHIP


> [!NOTE] Так
> Параметр cascade_delete устанавливается в Relationship() для модели, у которой нет внешнего ключа
> 
> cascade_delete необходим, чтобы сообщить SQLAlchemy, что он должен удалить объекты Python в памяти.
> 
> cascade_delete помещается в Relationship(). Обычно на стороне «одна» в отношениях «один-ко-многим», стороне без внешнего ключа.

```python
class Category(SQLModel, table=True):
	id: int | None = Field(default=None, primary_key=True)
	title: str

	products: list["Product"] = Relationship(back_populates="category", cascade_delete=True)
```
- Включение каскадного удаления. При исходной записи будут автоматически удаляться связанные записи

### ONDELETE

Параметр ondelete устанавливает соответствующее значение в столбце внешнего ключа в БД. Параметр может принимать следующие значения:
- cascade: Автоматически удалять эту запись при удалении связанной с ней
- set null: Установить для этого поля значение null при удалении связанной записи
- restrict: Запретить удаление этой записи, если существует значение внешнего ключа

> [!NOTE] Так
> Параметр ondelete устанавливается в поле() для модели, имеющей внешний ключ.
> 
> ondelete настроит БД на случай, если кто-то будет с ней напрямую взаимодействовать.
> 
> ondelete помещается в Field() с внешним ключом. На стороне «многих» в отношениях «один-ко-многим».


```python
class Category(SQLModel, table=True):
	...
	products: list["Product"] = Relationship(back_populates="category", cascade_delete=True)


class Product(SQLModel, table=True):
	...
	category_id: int | None = Field(default=None, foreign_key="category.id", ondelete="CASCADE")
	category: Category | None = Relationship(back_populates="products")
```
- БД будет автоматически удалять товары при удалении соответствующей категории

```python
statement = select(Category).where(Category.id == 1)
category = session.exec(statement).one()
session.delete(category)
session.commit()
```
- Теперь при удалении категории будут автоматически удалены соответствующие товары

### PASSIVE DELETE

```python
class Category(SQLModel, table=True):
	...
	products: list["Product"] = Relationship(back_populates="category", passive_deletes="all")


class Product(SQLModel, table=True):
	...
	category_id: int | None = Field(default=None, foreign_key="category.id", ondelete="SET NULL")
	category: Category | None = Relationship(back_populates="products")
```
- SET NULL указывает, что при удалении категории всем связанным продуктам будет присвоено NULL в поле category_id 

## MANY-TO-MANY

```python
class ProductCategoryLink(SQLModel, table=True):
	category_id: int | None = Field(default=None, foreign_key="category.id", primary_key=True)
	product_id: int | None = Field(default=None, foreign_key="product.id", primary_key=True)


class Category(SQLModel, table=True):
	...
	products: list["Product"] = Relationship(back_populates="categories", link_model=ProductCategoryLink)


class Product(SQLModel, table=True):
	...
	categories: list[Category] | None = Relationship(back_populates="products", link_model=ProductCategoryLink)
```
- Реализация отношения многие ко многим

### CREATE WITH M-T-M

```python
cat1 = Category(...)
cat2 = Category(...)

pr1 = Product(categories=[cat1, cat2])
pr2 = Product(categories=[cat1])

session.add(pr1)
session.add(pr2)
session.commit()
session.refresh(p1)
session.refresh(p2)
```
- Пример добавления данных

### UPDATE & REMOVE WITH M-T-M

```python
cat1.products.append(pr1)
session.add(cat1)
session.commit()

pr1.categories.remove(cat1)
```

## MANY TO MANY WITH EXTRA FIELDS

```python
class ProductCategoryLink(SQLModel, table=True):
	category_id: int | None = Field(default=None, foreign_key="category.id", primary_key=True)
	product_id: int | None = Field(default=None, foreign_key="product.id", primary_key=True)
	is_bool: bool = True

	category: "Category" = Relationship(back_populates="product_links")
	product: "Product" = Relationship(back_populates="category_link")


class Category(SQLModel, table=True):
	...
	product_links: list[ProductCategoryLink] = Relationship(back_populates="category")


class Product(SQLModel, table=True):
	...
	category_links: list[ProductCategoryLink] | None = Relationship(back_populates="product")
```
- Модели

```python
cat1 = Category(...)
cat2 = Category(...)

prod1 = Product(...)
prod2 = Product(...)

cat1_link = ProductCategoryLink(category=cat1, product=prod1)
cat2_link = ProductCategoryLink(category=cat2, product=prod1)
cat3_link = ProductCategoryLink(category=cat1, product=prod2)

session.add(cat1_link)
...
session.commit()
```
- Create

```python
cat1.product_links.append(cat1_link)
```
- Add