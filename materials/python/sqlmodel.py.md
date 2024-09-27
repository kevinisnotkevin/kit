# SQLModel

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

## Фильтрация данных