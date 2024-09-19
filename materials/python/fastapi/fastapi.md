# FastAPI

Сурс: https://fastapi.tiangolo.com

FastAPI - веб-фреймворк для создания API с помощью Python на основе аннотаций типов Python. Основан на Scarlette и Pydantic. Обладает высокой производительностью, на уровне NodeJS и Go (благодаря Starlette и Pydantic).

FastAPI генерирует `schema` со всеми API, используя стандарт OpenAPI для определения API.

Имеет встроенную автоматическую документацию от Swagger UI (/docs) и ReDoc (/redoc).

Schema - это определение или описание чего-либо. Не код, который это реализует, а просто абстрактное описание.

OpenAPI - это спецификация, определяющая, как определить схему API.

API schema - пути API, возможные параметры, которые они принимают, и т. д.

Data schema - атрибуты JSON, типы данных, которые они имеют, и т. д.

OpenAPI определяет схему API. И эта схема включает определения (или schemas) данных, отправляемых и получаемых API с использованием **JSON schema**, стандарта для **JSON data schema**.

FastAPI автоматически генерирует JSON (необработанная схема OpenAPI) с описаниями всех API (доступно по /openapi.json). **OpenAPI schema** - это то, что лежит в основе двух включенных систем интерактивной документации.

# Установка

```bash
uv pip install fastapi[standard]
```
- Установка FastAPI с помощью `pip install fastapi[standard]` с некоторыми дополнительными стандартными зависимостями по умолчанию. Если вы не хотите иметь эти дополнительные зависимости, вы можете вместо этого установить `pip install fastapi`

# Запуск сервера

```bash
# Run fastapi in production mode
fastapi run

# Run fastapi in development mode
fastapi dev
```
- Запуск FastAPI

# Предопределенные значения

```python
from enum import Enum

from fastapi import FastAPI


class ModelName(str, Enum):
	aa = "aa"
	bb = "bb"
	cc = "cc"


app = FastAPI()


@app.get("/models/{model_name}")
async def get_model(model_name: ModelName): return
```
- Эндпоинт `get_model` получает предустановленные значения, в противном случае вызывает ошибку
 
# Модель - параметр запроса

```python
@app.post("/items/")
async def create_item(item: Item): return item
```
- Указание модели в качестве типа параметра. Такая функция принимает один параметр и читает тело запроса как JSON, приводит к соответствующим типам, проверяет на корректность данных, складывает полученные данные в параметр item

![](materials/images/python/fastapi_body_1.png)
- Созданная модель будет частью схемы JSON, сгенерированной OpenAPI. Также она будет указана в документации по API внутри каждой операции пути, в которой используется

# Тело запроса + path-параметры + query-параметры

```python
@app.put("/items/{item_id}")
async def foo(item_id: int, item: Item, q: str | None = None): return
```
- FastAPI позволяет одновременно обьявлять параметры пути, запроса и тело запроса

Параметры функции распознаются следующим образом:
- Если параметр также указан в пути, то он будет использоваться как параметр пути
- Если аннотация типа параметра содержит примитивный тип (`int`, `float`, `str`, `bool` и т.п.), он будет интерпретирован как параметр запроса
- Если аннотация типа параметра представляет собой модель Pydantic, он будет интерпретирован как параметр тела запроса

# Query-параметры

```python
@app.get("/")
async def foo(a: int = 0, b: int = 0): return
```
- Функция принимает query-параметры типа int (?a=10&b=20). Параметры функции, которые не являются параметрами пути, автоматически интерпретируются как query-параметры

```python
from fastapi import Query
```
- Обьект позволяет добавлять валидацию и метаданные для query-параметров

## Валидация строк

```python
q: str | None = None
q: Annotated[str | None] = None
```
- Параметр q может быть строкой или None, по умолчанию None. Обе версии означают одно и то же. Annotated позволяет добавить больше метаданных

```python
from typing import Annotated

from fastapi import FastAPI, Query


@app.get("/items/")
async def foo(
	q: Annotated[str | None, 
	Query(max_length=50, min_length=1, pattern="^fixedquery$")] = None
): return
```
- Функция принимает необязательный параметр q. Если параметр q не None, тогда будет проверяться, что его длина меньше 50 символов, больше 1 символа, соответствует регулярному выражению

```python
@app.get("")
async def foo(q: Annotated[str, Query(min_length=3)]): return


@app.get("")
async def foo(q: Annotated[str, Query(min_length=3)] = ...): return


from pydantic import Required


@app.get("")
async def foo(q: Annotated[str, Query(min_length=3)] = Required): return
```
- Все эти варианты идентичны по функциональности. Функция принимает обязательный параметр. Многоточие указывает на то, что параметр является обязательным, так же как и обьект Required

## Множество значений для query-параметра

```python
from typing import Annotated

from fastapi import FastAPI, Query


app = FastAPI()


@app.get("/")
async def foo(q: Annotated[list[str] | None, Query()] = None): return
```
- Функция принимает параметр, который может быть указан в URL несколько раз (/?q=foo&q=bar). Чтобы обьявить query-параметр типом list нужно использовать Query, иначе он будет интерпретирован как тело запроса

## Больше метаданных о query-параметрах

```python
from typing import Annotated

from fastapi import FastAPI, Query


@app.get("/")
async def foo(
	q: Annotated[
		str | None, 
		Query(
			title="Query title",
			description="Query desc",
			alias="Query alias",
			deprecated=True,
			include_in_schema=False
		),
	] = None,
)
```
- Указание названия, описания, псевдонима query-параметра. Псевдоним позволяет изменить название параметра в запросе. Параметр deprecated позволяет указать в документации, что параметр является устаревшим, но такой параметр все еще используется. Параметр include_in_schema позволяет исключить query-параметр из схемы и документации

## Модели query-параметров

Если есть группа связанных параметров запроса, то можно создать модель Pydantic для их обьявления.

```python
from fastapi import FastAPI, Query
from pydantic import BaseModel, Field

from typing import Annotated, Literal


app = FastAPI()


class FilterParams(BaseModel):
	model_config = {"extra": "forbid"}  # Не обязательно

	limit: int = Field(100, gt=0, le=100)
	offset: int = Field(0, ge=0)
	order_by: Literal["created_at", "updated_at"] = "created_at"
	tags: list[str] = []


@app.get("/items/")
async def item_list(filter_query: Annotated[FilterParams, Query()]): return
```
- Функция принимает четыре query-параметра, pydantic обрабатывает данные и возвращает модель. С помощью model_config можно настроить модель pydantic так, что, если в запросе будут переданы дополнительные параметры, не входящие в модель, то будет возвращена ошибка

# Path-параметры

```python
@app.get("/{item_id}")
async def foo(item_id: int): return
```
- Функция принимает path-параметр (/1) типа int. Используя определениям типов, выполняется автоматический парсинг запросов

```python
from fastapi import Path
```
- Обьект позволяет добавлять валидацию и метаданные для path-параметров

```python
from fastapi import FastAPI, Path

from typing import Annotated


fastapi = FastAPI()


@app.get("/{item_id}")
async def foo(
	item_id: Annotated[int, Path(title="Path title")],
): return
```
- Обьект Path принимает те же параметры, что и Query. Path-параметр всегда является обязательным

## Валидация числовых данных

```python
@app.get("/{item_id}")
async def foo(item_id: Annotated[int, Path(ge=1, lt=1000)]): return
```
- Функция принимает path-параметр, который должен быть больше или равен 1 и меньше 1000. Валидация также применима к значениям типа float

```
gt - больше
ge - больше или равно
lt - меньше
le - меньше или равно
```

# Body-параметры

Чтобы обьявить тело запроса, необходимо использовать модели Pydantic.

```python
from pydantic import BaseModel


class Item(BaseModel):
	name: str
	description: Union[str, None] = None
	price: float


@app.post("/")
async def foo(item: Item): pass
```
- Функция принимает body-параметр. Если параметр имеет значение по умолчанию, то он является необязательным

```python
from fastapi import FastAPI
from pydantic import BaseModel


class User(BaseModel):
	username: str
	full_name: str | None = None


class Item(BaseModel):
	name: str
	description: str | None = None
	price: float


@app.put("/")
async def foo(user: User, item: Item): return
```
- Функция принимает множество параметров тела запроса

```json
{
	"user": {
		"username": "username",
		"full_name": ""
	},
	"item": {
		"name": "item",
		"description": "",
		"price": 1000
	}
}
```
- Будет ожидаться запрос следующего формата. Произойдет автоматическое преобразование из запроса, а также проверка данных, создание документации и схемы. В итоге каждый параметр функции получит свой контент

```python
from fastapi import Body
```
- Обьект, который аналогичен Query и Path обьектам, но позволяет определить дополнительные данные для body-параметров. Имеет все те же параметры валидации и метаданных, что и Query и Path

```python
from fastapi import FastAPI, Body
from typing import Annotated


app = FastAPI()


@app.put("/")
async def foo(var: Annotated[int, Body()]): return
```
- Функция ожидает body-параметр var типа int

```python
item: Annotated[Item, Body(embed=True)]
```
- Представим, что это параметр функции. Он ожидает получить body-параметр с ключом item

```json
{
    "item": {...}
}
// Без параметра embed в обьекте Body:
{...}
```
- Ожидаемый контент параметра item

# Fields

```python
from pydantic import Fields
```
- Обьект позволяет обьявлять дополнительную валидацию и метаданные внутри pydantic моделей. Имеет те же возможности, что и Body, Query, Path

```python
from pydantic import BaseModel, Fields


class Item(BaseModel):
	name: str
	description: str | None = Field(
		default=None, title="Title", max_length=300
	)
	price: float = Field(gt=0, desctiption="Desc")
```

# Вложенные модели

```python
from pydantic import BaseModel, HttpUrl


class Image(BaseModel):
	url: HttpUrl
	name: str


class Item(BaseModel):
	name: str
	desc: str | None = None
	tags: set[int] = set()
	image: Image | None = None
```
- Модель содержит поле tags, которое является типом set и может содержать в себе обьекты типа int. Также модель содержит поле image, которое является обьектом модели Image. Модель Image имеет поле url, которое является обьектом модели HttpUrl (Автоматическая проверка на соответствие допустимому URL-адресу). Pydantic позволяет определять модели с произвольным уровнем вложенности

Такая реализация предоставляет:
- Поддержку редакторов
- Преобразование данных
- Валидацию данных
- Автоматическую документацию

Также можно обьявлять тело запроса как dict с ключами определенного типа и значениями другого типа данных. НО! JSON поддерживает ключи только типа str.

# Обьявление примера запроса данных

```python
from pydantic import BaseModel


class Item(BaseModel):
	name: str
	price: float

	model_config = {
		"json_schema_extra": {
			"examples": [
				{
					"name": "name",
					"price": 1000
				}
			]
		}
	}
```
- Обьявление ключа example в конфиге модели pydantic добавляет информацию в JSON схему выходных данных для этой модели, которая будет использоваться в документации к API

```python
from pydantic import BaseModel, Field


class Item(BaseModel):
	name: str
	desc: str | None = Field(default=None, examples=["Desc for item..."])
	price: float = Field(examples=[1000])
```
- Использование аргумента examples в Field в моделях pydantic добавляет информацию для документации

# Cookie-параметры

```python
from fastapi import Cookie, FastAPI


app = FastAPI()


@app.get("/")
async def foo(ads_id: str | None = Cookie(default=None)): return
```
- Обьект позволяет обьявлять cookie

## Модель cookie-параметров

FastAPI поддерживает обьединение cookie в одну группу в модель pydantic. Работает аналогично Query, Cookie, Header.

```python
from pydantic import BaseModel
from fastapi import FastAPI, Cookie

from typing import Annotated


app = FastAPI()


class Cookies(BaseModel):
	session_id: str
	tracker: str | None = None


@app.get("/")
async def foo(cookies: Annotated[Cookies, Cookie()]): return
```
- Создание модели pydantic с определенными параметрами cookie. Обьявление параметра функции как Cookie типа Cookies. Данные для каждого поля из модели кук будут извлечены и обработаны

# Header-параметры

```python
from fastapi import FastAPI, Header

from typing import Annotated


app = FastAPI()


@app.get("/")
async def foo(user_agent: Annotated[str | None, Header()] = None): return
```
- Обьект позволяет обьявлять заголовки

По умолчанию Header преобразует символы имен параметров из подчеркивания в дефис для извлечения и документирования заголовков. HTTP-заголовки не чувствительны к регистру.

Пример заголовка: user_agent (Вместо User-Agent).

```python
from typing import Annotated

from fastapi import FastAPI, Header


app = FastAPI()


@app.get("/")
async def foo(x_token: Annotated[list[str] | None, Header()] = None): return
```
- Функция может получать несколько заголовков с одним и тем же именем, но разными значениями

## Модель header-параметров

Работает так же как и Cookie, Query.

```python
from typing import Annotated

from fastapi import FastAPI, Header
from pydantic import BaseModel


app = FastAPI()


class Headers(BaseModel):
    host: str
    x_tag: list[str] = []


@app.get("/")
async def foo(headers: Annotated[Headers, Header()]): return
```
- Аналогия работы cookie-параметров