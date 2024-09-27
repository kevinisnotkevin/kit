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

## Установка

```bash
uv pip install fastapi[standard]
```
- Установка FastAPI с помощью `pip install fastapi[standard]` с некоторыми дополнительными стандартными зависимостями по умолчанию. Если вы не хотите иметь эти дополнительные зависимости, вы можете вместо этого установить `pip install fastapi`

## Запуск сервера

```bash
# Run fastapi in production mode
fastapi run

# Run fastapi in development mode
fastapi dev
```
- Запуск FastAPI

## Предопределенные значения

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
 
## Модель - параметр запроса

```python
@app.post("/items/")
async def create_item(item: Item): return item
```
- Указание модели в качестве типа параметра. Такая функция принимает один параметр и читает тело запроса как JSON, приводит к соответствующим типам, проверяет на корректность данных, складывает полученные данные в параметр item

![](materials/images/python/fastapi_body_1.png)
- Созданная модель будет частью схемы JSON, сгенерированной OpenAPI. Также она будет указана в документации по API внутри каждой операции пути, в которой используется

## Тело запроса + path-параметры + query-параметры

```python
@app.put("/items/{item_id}")
async def foo(item_id: int, item: Item, q: str | None = None): return
```
- FastAPI позволяет одновременно обьявлять параметры пути, запроса и тело запроса

Параметры функции распознаются следующим образом:
- Если параметр также указан в пути, то он будет использоваться как параметр пути
- Если аннотация типа параметра содержит примитивный тип (`int`, `float`, `str`, `bool` и т.п.), он будет интерпретирован как параметр запроса
- Если аннотация типа параметра представляет собой модель Pydantic, он будет интерпретирован как параметр тела запроса

## Query-параметры

```python
@app.get("/")
async def foo(a: int = 0, b: int = 0): return
```
- Функция принимает query-параметры типа int (?a=10&b=20). Параметры функции, которые не являются параметрами пути, автоматически интерпретируются как query-параметры

```python
from fastapi import Query
```
- Обьект позволяет добавлять валидацию и метаданные для query-параметров

### Валидация строк

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

### Множество значений для query-параметра

```python
from typing import Annotated

from fastapi import FastAPI, Query


app = FastAPI()


@app.get("/")
async def foo(q: Annotated[list[str] | None, Query()] = None): return
```
- Функция принимает параметр, который может быть указан в URL несколько раз (/?q=foo&q=bar). Чтобы обьявить query-параметр типом list нужно использовать Query, иначе он будет интерпретирован как тело запроса

### Больше метаданных о query-параметрах

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

### Модели query-параметров

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

## Path-параметры

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

### Валидация числовых данных

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

## Body-параметры

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

## Fields

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

## Вложенные модели

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

## Обьявление примера запроса данных

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

## Cookie-параметры

```python
from fastapi import Cookie, FastAPI


app = FastAPI()


@app.get("/")
async def foo(ads_id: str | None = Cookie(default=None)): return
```
- Обьект позволяет обьявлять cookie

### Модель cookie-параметров

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

## Header-параметры

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

### Модель header-параметров

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

## Возвращаемые типы

```python
from pydantic import BaseModel
from fastapi import FastAPI


app = FastAPI()


@app.get("/")
async def foo() -> list[Item]: pass
```
- Функция возвращает список обьектов Item. Можно обьявить тип возвращаемого значения функции эндпоинта, так же как и для входных данных в параметрах функций. Такая запись позволяет FastAPI валидировать ответ, генерировать автоматическую документацию. В ответе останутся только те данные, которые определены

Используйте параметр response_model у декоратора операции пути для того, чтобы задать модель ответа и в большей степени для того, чтобы быть уверенным, что приватная информация будет отфильтрована.

А также используйте response_model_exclude_unset, чтобы возвращать только те значения, которые были заданы явно.

### Одна модель для запроса, вторая для ответа

```python
from typing import Any

from fastapi import FastAPI
from pydantic import BaseModel, EmailStr


app = FastAPI()


class UserIn(BaseModel):
    username: str
    password: str
    email: EmailStr
    full_name: str | None = None


class UserOut(BaseModel):
    username: str
    email: EmailStr
    full_name: str | None = None


@app.post("/user/", response_model=UserOut)
async def create_user(user: UserIn) -> Any:
    return user
```
- Функция принимает данные пользователя и возвращает их без пароля. Эта реализация фильтрует ответ и исключает все, что не указано в выходной модели. В примере модели входных данных и выходных данных различаются. И если указать аннотацию типа выходного значения функции как UserOut - проверка типов выдаст ошибку из-за того, что мы возвращаем некорректный тип. Поскольку это 2 разных класса. Поэтому в примере мы можем объявить тип ответа только в параметре response_model.

```python
from fastapi import FastAPI
from pydantic import BaseModel, EmailStr


app = FastAPI()


class BaseUser(BaseModel):
    username: str
    email: EmailStr
    full_name: str | None = None


class UserIn(BaseUser):
    password: str


@app.post("/user/")
async def create_user(user: UserIn) -> BaseUser:
    return user
```
- Использование наследования, чтобы пользоваться преимуществами аннотаций типов и получение более полной статической проверки типов, а также получение фильтрации ответа

## Дополнительные модели

```python
from fastapi import FastAPI
from pydantic import BaseModel, EmailStr


app = FastAPI()


class UserBase(BaseModel):
    username: str
    email: EmailStr
    full_name: str | None = None


class UserIn(UserBase):
    password: str


class UserOut(UserBase):
    pass


class UserInDB(UserBase):
    hashed_password: str


def fake_password_hasher(raw_password: str):
    return "supersecret" + raw_password


def fake_save_user(user_in: UserIn):
    hashed_password = fake_password_hasher(user_in.password)
    user_in_db = UserInDB(**user_in.dict(), hashed_password=hashed_password)
    print("User saved! ..not really")
    return user_in_db


@app.post("/user/", response_model=UserOut)
async def create_user(user_in: UserIn):
    user_saved = fake_save_user(user_in)
    return user_saved
```
- Использование несколько связанных между собой моделей также применимо в модели пользователя. Например, модель для ввода должна содержать пароль, модель для вывода не должна содержать пароль, а модель для БД должна содержать хеш пароля

## HTTP статус коды

```python
@app.post("/", status_code=201)
async def foo(): return
```
- Эндпоинту назначен статус код ответа 201

```python
from fastapi import status
from scarlette import status

var = status.HTTP_201_CREATED
```
- Переменная содержит то же числовое значение, что и в прошлом примере

## Form

```bash
uv pip install python-multipart
```
- Когда нужно использовать поля формы вместо JSON, то можно использовать Form. Для этого нужно установить модуль python-multipart

```python
from typing import Annotated

from fastapi import FastAPI, Form


app = FastAPI()


@app.post("/")
async def foo(username: Annotated[str, Form()], password: Annotated[str, Form()]): return
```
- Функция принимает два параметра, которые являются обьектом Form. Данный способ требует отправку данных для авторизации посредством формы (а не JSON)  обязательно наличия в форме срого именованных полей username и password
- Form это класс, который наследуется от Body, а значит поддерживает все те же настройки

Данные из форм обычно кодируются с использованием типа медиа application/x-www-form-urlencoded. Когда форма содержит файлы, она кодируется как multipart/form-data

### Form-модели

Можно использовать модели Pydantic для обьявления полей формы.

```python
from typing import Annotated
from pydantic import BaseModel
from fastapi import FastAPI, Form

app = FastAPI()


class FormData(BaseModel):
	username: str
	password: str

@app.post("/login")
async def login(data: Annotated[FormData, Form()]): return
```
- Нужно обьявить модель Pydantic с полями, которые нужно получать в качестве полей формы, а затем обьявить параметр как форму. Функция извлечет данные для каждого поля из данных формы в запросе и предоставит модель Pydantic

## Загрузка файлов

```python
from typing import Annotated

from fastapi import FastAPI, File, UploadFile

app = FastAPI()


@app.post("/files")
async def create_file(file: Annotated[bytes, File()]): return

@app.post("/upload_file")
async def create_upload_file(file: UploadFile): return
```
- Классы File, UploadFile позволяют загружать файлы. File наследуется от Form
- File: функция получит содержимое файла в байтах. Содержимое будет храниться в памяти. Подходит для небольших файлов
- UploadFile: Файл хранится в памяти до определнного предела размера, после он хранится на диске. Это позволяет работать с большими файлами, не используя всю память. Из загруженного файла можно получить метаданные

### UploadFile

UploadFile имеет атрибуты:
- filename - строка с исходным именем файла, который был загружен
- content_type - строка с типом содержимого MIME
- file - (SpooledTemporaryFile) файл Python, который можно передавать другим функциям, ожидающим файл в качестве обьекта

UploadFile имеет async методы:
- write(data) - записать данные в файл
- read(size) - прочитать количество байт из файла
- seek(offset) - перейти к байту на позииции в файле
- close() - закрыть файл

```python
from typing import Annotated
from fastapi import FastAPI, File, UploadFile


app = FastAPI()

@app.post("/post")
async def create_file(file: Annotated[bytes | None, File(description="desc")]): return


@app.post("/upload_file")
async def create_upload_file(file: UploadFile | None = None): return
```
- Реализация необязательной загрузки файлов, используя стандартные аннотации типов

### Загрузка нескольких файлов

```python
from typing import Annotated

from fastapi import FastAPI, File, UploadFile
from pydantic import BaseModel


app = FastAPI()


@app.post("/files/")
async def create_files(files: Annotated[list[bytes], File()]: return


@app.post("/upload_file")
async def create_upload_files(files: list[UploadFile]): return
```
- Можно одновременно загружать несколько файлов. Они связаны с одним и тем же полем формы, отправляемым с помощью данных формы. Для этого необходимо обьявить список bytes или UploadFile

## Файлы и формы в запросе

```python
from typing import Annotated

from fastapi import FastAPI, File, UploadFile, Form

app = FastAPI()


@app.post("/files/")
async def create_file(
	file: Annotated[bytes, File()],
	fileb: Annotated[UploadFile, File()],
	token: Annotated[str, Form()]
): return
```
- Файлы и поля формы будут загружены в виде данных формы.

## Обработка ошибок

```python
from fastapi import FastAPI, HTTPException

app = FastAPI()
items = {}

@app.get("/items/{item_id}")
async def read_item(item_id: str):
	if item_id not in items:
		raise HTTPException(
			status_code=404, 
			detail="description",
			headers={"X-Error": "Err"},
		)
	return {}
```
- Если запросить элемент по несуществующему номеру, то возникнет исключение со статус-кодом 404. Можно добавить заголовки ответа с помощью headers в HTTPException

```python
from fastapi import FastAPI, Request
from fastapi.responses import JSONResponse

class UnicornException(Exception):
	def __init__(self, name: str):
		self.name = name


app = FastAPI()


@app.exception_handler(UnicornException)
async def unicorn_exception_handler(request: Request, exc: UnicornException):
	return JSONResponse(
		status_code=418,
		content={"message": f"Err {exc.name}"}
	)


@app.get("/unicorns/{name}")
async def read_unicorn(name: str):
	if name == "name":
		raise UnicornException(name=name)
```
- Добавление собственного обработчика исключений с помощью app.excpetion_handler

```python
from fastapi import FastAPI, HTTPException
from fastapi.exceptions import RequestValidationError
from fastapi.responses import PlainTextResponse
from scarlette.exceptions import HTTPException as ScarletteHTTPException

app = FastAPI()

@app.exception_handler(ScarletteHTTPException)
async def http_exception_handler(request, exc):
	return PlainTextReponse(str(exc.detail), status_code=exc.status_code)


@app.exception_handler(RequestValidationError)
async def validation_exception_handler(request, exc):
	return PlainTextResponse(str(exc), status_code=400)

@app.get("/items/{item_id}")
async def read_item(item_id: int):
	raise HTTPException(status_code=418, detail="Detail")
```
- Переопределение исключения недопустимых данных RequestValidatorError. Обработчик исключения получает обьект Request и исключение. Теперь если перейти по несуществующему эндпоинту, то будет выведена текстовая версия сообщения об ошибке (PlainTextReponse), а не JSON
- Переопределение обработчика ошибок HTTPException. Ошибки будут возвращаться в виде текста, а не JSON

## Конфигурация операций пути

```python
from fastapi import FastAPI, status


@app.get("/", status_code=201)
async def foo(): return

@app.get("/", status_code=status.HTTP_201_CREATED)
async def foo(): return
```
Можно определить status_code, который будет использоваться в ответах эндпоинта

```python
@app.get("/", tags=["items"])
async def foo(): return
```
- Добавление тега

```python
@app.get("/", summary="...", description="...")
async def foo(): return

@app.get("/")
async def foo():
	"""
	# Description with Markdown
	"""
	return
```
- Добавление краткого и развернутого описания эндпоинта. Также описание можно добавить в строке документации функции

```python
@app.get("/", response_description="...")
async def foo(): return
```
- Добавление описания ответа

```python
@app.get("/", deprecated=True)
async def foo(): return
```
- Обозначение эндпоинта, как устаревшего

## JSON кодировщик

```python
from fastapi.encoders import jsonable_encoder


@app.put("/items/{id}")
def update_item(id: str, item: Item):
    json_compatible_item_data = jsonable_encoder(item)
```
- Функция принимает обьект, например модель pydantic, и возвращает его версию, совместимую с JSON. Например, pydantic модель будет преобразована в dict, а datetime в str
- Это может быть полезно для добавления данных в БД, которая принимает только JSON-совместимые данные
- Результат функции - обьект, который может быть закодирован с помощью json.dumps()
- Эта функция используется внутри системы для преобразования данных

## Dependency injection

```python
from typing import Annotated

from fastapi import Depends, FastAPI


app = FastAPI()


async def common_parameters(q: str | None = None, skip: int = 0, limit: int = 100):
	return {"q": q, "skip": skip, "limit": limit}


@app.get("/")
async def read_items(commons: Annotated[dict, Depends(common_parameters)]):
	return commons


@app.get("/")
async def read_users(commons: Annotated[dict, Depends(common_parameters)]):
	return commons
```
- Функция, которая может принимать все те же параметры, что и эндпоинты

В приведенном примере есть повторение кода. Можно использовать Annotated в переменной, которую можно использовать в нескольких местах

```python
CommonsDep = Annotated[dict, Depends(common_parameters)]


@app.get("/items/")
async def read_items(commons: CommonsDep):
    return commons


@app.get("/users/")
async def read_users(commons: CommonsDep):
    return commons
```
- Пример без дублирования

```python
class CommonQueryParams:
	def __init__(self, q: : str | None = None, skip: int = 0, limit: int = 100):
	self.q = q
	self.skip = skip
	self.limit = limit


@app.get("/")
async def foo(commons: Annotated[CommonQueryParams, Depends(CommonQueryParams)]): return
```
- Depends может принимать любой вызываемый обьект. Поэтому можно передать класс. Эндпоинт в этом примере принимает те же параметры, что и в предыдущем

```python
from typing import Annotated
from fastapi import FastAPI, Cookie, Depends

app = FastAPI()


def query_extractor(q: str | None = None):
	return q


def query_or_cookie_extractor(
	q: Annotated[str, Depends(query_extractor)],
	last_query: Annotated[str | None, Cookie()] = None,
): return


@app.get("/")
async def foo(query_or_default: Annotated[str, Depends(query_or_extractor)]): return
```
- Использование подзависимости. Если пользователь не указал параметр q в запросе, то использовается последний использованный запрос, который был ранее сохранен в куки-параметре

### Зависимости в декораторе эндпоинта

```python
from typing import Annotated

from fastapi import FastAPI, Depends, Header, HTTPException


app = FastAPI()


async def verify_token(x_token: Annotated[str, Header()]):
	if x_token != "token":
		raise HTTPException(status_code=400, detail="X-Token invalid")


async def verify_key(x_key: Annotated[str, Header()]):
	if x_key != "key":
		raise HTTPException(status_code=400, detail="X-Key invalid")


@app.get("/", dependencies=[Depends(verify_token), Depends(verify_key)]):
async def read_items(): return
```
- Когда возвращаемое значение зависимости не используется внутри функции эндпоинта или же зависимость не возвращает значения, то зависимости могут быть добавлены списком dependencies в декораторе эндпоинта. Если такие зависимости будут возвращать значения, то эти значения использоваться не будут

### Глобальные зависимости

```python
from typing import Annotated

from fastapi import FastAPI, Depends, Header


async def verify_token(x_token: Annotated[str, Header()]): ...


app = FastAPI(dependencies=[Depends(verify_token)])
```
- Такая зависимость применяется ко всем эндпоинтам в приложении

### Зависимости с yield

```python
async def get_db():
	db = DBSession()
	try:
		yield db
	finally:
		db.close()
```
- Можно создать зависимости, которые выполняют некоторые действия после завершения работы. Например, можно создать сессию работыс БД и закрыть после завершения. Перед созданием ответа будет выполнен код до yield включая. Остальной код выполнится после доставки ответа

## Безопасность

```python
from typing import Annotated

from fastapi import FastAPI, Depends
from fastapi.security import OAuth2PasswordBearer


app = FastAPI()


oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")


@app.get("/")
async def foo(token: Annotated[str, Depends(oauth2_scheme)]): return
```
- Внедрение аутентификации по паролю. OAuth2 разработан, чтобы api был независим от сервера, который аутентифицирует пользователей. В данном случае это один и тот же сервер
- OAuth2PasswordBearer - класс, который реализует OAuth2 с аутентификацией по паролю, используя токен Bearer. Параметр tokenUrl содержит url (/token), который будет использоваться для отправки данных для получения токена
- Эндпоинт foo будет искать заголовок Authorization и проверять, содержит ли он значение Bearer с токеном. Если запрос не содержит валидного токена, то будет возвращена ошибка 401

### Настройка авторизации

```python
from typing import Annotated

from fastapi import FastAPI, Depends
from fastapi.security import OAuth2PasswordBearer
from pydantic import BaseModel


app = FastAPI()

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")


class User(BaseModel):
	username: str
	email: str | None = None
	full_name: str | None = None
	disabled: bool | None = None


def fake_decode_token(token):
	return User(
		username=token+"fakedecoded", email="test@example.com", full_name="User"
	)


async def get_current_user(token: Annotated[str, Depends(oauth2_scheme)]):
	user = fake_decode_token(token)
	return user


@app.get("/users/me")
async def read_users_me(current_user: Annotated[User, Depends(get_current_user)]):
	return current_user
```
- Создание модели пользователя
- Зависимость get_current_user получает токен из подзависимости oauth2_scheme. Далее она передает токен в fake_decode_token, которая возвращает модель User pydantic
- Эндпоинт позволяет получить текущего пользователя 

### OAuth2 with Password and Bearer

```python
from typing import Annotated

from fastapi import Depends, FastAPI
from fastapi.security import OAuth2PasswordBearer, OAuth2PasswordRequestForm
from pydantic import BaseModel


fake_users_db = {...}

app = FastAPI()


def fake_hash_password(password: str):
    return "fakehashed" + password


oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")


class User(BaseModel):
	username: str
	email: str | None = None
	full_name: str | None = None
	disabled: bool | None = None



class UserInDB(User):
    hashed_password: str


def get_user(db, username: str):
    if username in db:
        user_dict = db[username]
        return UserInDB(**user_dict)


def fake_decode_token(token):
	return User(
		username=token+"fakedecoded", email="test@example.com", full_name="User"
	)


async def get_current_user(token: Annotated[str, Depends(oauth2_scheme)]):
    user = fake_decode_token(token)
    if not user:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Invalid authentication credentials",
            headers={"WWW-Authenticate": "Bearer"},
        )
    return user


async def get_current_active_user(
    current_user: Annotated[User, Depends(get_current_user)],
):
    if current_user.disabled:
        raise HTTPException(status_code=400, detail="Inactive user")
    return current_user


@app.post("/token")
async def login(form_data: Annotated[OAuth2PasswordRequestForm, Depends()]):
    user_dict = fake_users_db.get(form_data.username)
    if not user_dict:
        raise HTTPException(status_code=400, detail="Incorrect username or password")
    user = UserInDB(**user_dict)
    hashed_password = fake_hash_password(form_data.password)
    if not hashed_password == user.hashed_password:
        raise HTTPException(status_code=400, detail="Incorrect username or password")

    return {"access_token": user.username, "token_type": "bearer"}


@app.get("/users/me")
async def read_users_me(
    current_user: Annotated[User, Depends(get_current_active_user)],
):
    return current_user
```
- OAuth2PasswordRequestForm - зависимость класса, которая обьявляет тело формы с помощью username, password, scope, grant_type

## Middleware

```python
import time

from fastapi import FastAPI, Request


app = FastAPI()


@app.middleware("http")
async def add_process_time_header(request: Request, call_next):
	start_time = time.perf_counter()
	response = await call_next(request)
	process_time = time.perf_counter() - start_time
	response.headers["X-Process-Time"] = str(process_time)
	return response
```
- Middleware принимает обьект Response и функцию, которая соответствует эндпоинту. Засекается время, выполняется эндпоинт и возвращается его ответ в middleware. Далее в этот ответ добавляется заголовок с вычисленным временем ответа и отправляется клиенту

## CORS

```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware


app = FastAPI()


origins = [
	"http://localhost"
]

app.add_middleware(
	CORSMiddleware,
	allow_origins=origins,
	allow_credentials=True,
	allow_methods=["*"],
	allow_headers=["*"],
)


@app.get("/")
async def main(): return
```
- Настройка CORS механизма. Поддерживаются следующие аргументы:
- `allow_origins` - Список источников, на которые разрешено выполнять кросс-доменные запросы. Например, `['https://example.org', 'https://www.example.org']`. Можно использовать `['*']`, чтобы разрешить любые источники
- `allow_origin_regex` - Регулярное выражение для определения источников, на которые разрешено выполнять кросс-доменные запросы. Например, `'https://.*\.example\.org'`
- `allow_methods` - Список HTTP-методов, которые разрешены для кросс-доменных запросов. По умолчанию равно `['GET']`. Можно использовать `['*']`, чтобы разрешить все стандартные методы
- `allow_headers` - Список HTTP-заголовков, которые должны поддерживаться при кросс-доменных запросах. По умолчанию равно `[]`. Можно использовать `['*']`, чтобы разрешить все заголовки. Заголовки `Accept`, `Accept-Language`, `Content-Language` и `Content-Type` всегда разрешены для простых CORS-запросов
- `allow_credentials` - указывает, что куки разрешены в кросс-доменных запросах. По умолчанию равно `False`. Также, `allow_origins` нельзя присвоить `['*']`, если разрешено использование учётных данных. В таком случае должен быть указан список источников
- `expose_headers` - Указывает любые заголовки ответа, которые должны быть доступны браузеру. По умолчанию равно `[]`
- `max_age` - Устанавливает максимальное время в секундах, в течение которого браузер кэширует CORS-ответы. По умолчанию равно `600`

## APIRouter

```python
from fastapi import APIRouter

router = APIRouter(
	prefix="/home",
	tags=["home"],
	dependencies=[Depends(...)],
)


@router.get("/")
async def foo(): return
```
- APIRouter используется для организации кода и будет включен в основное приложение FastAPI. APIRouter может принимать различные параметры. Благодаря префиксу /home все эндпоинты от этого роута будут начинаться с /home. Список тегов будет применяться ко всем эндпоинтам этого роута, также как и список зависимостей

```python
from fastapi import FastAPI

from app.routers import users


app = FastAPI()
app.include_router(users.router)
app.include_router(
	users.router,
	prefix="/users",
)
```
- Добавление роутов в корень приложения. Параметры роутов можно обьявлять и таким образом. Такая запись не влияет на производительность апи

## Фоновые задачи

Фоновые задачи - задачи, которые выполняются после возвращения ответа сервером. 

Например, отправка писем на почту, обработка данных.

Если нужно выполнить сложные вычисления и необязательно в одном процессе с FastAPI, то можно воспользоваться Celery. Но, если нужен общий доступ к общим переменным и обьектам апи или нужно выполнять простые фоновые задачи, то можно использовать BackgroundTasks.

```python
from fastapi import FastAPI, BackgroundTasks


app = FastAPI()


def send_message(email): return


@app.get("")
async def send(email: str, background_tasks: BackgroundTasks):
	background_tasks.add(write_notification, email)
	return
```
- Добавляем в фукнцию параметр типа BackgroundTasks. Фоновая функция может быть синхронной или асинхронной. Чтобы добавить фоновую задачу, необходимо вызвать метод add_task() у обьекта backgroud_tasks и передать функцию, которая будет выполнена в фоне

## Метаданные документации

```python
app = FastAPI(
	title="Title",
	description="Description with Markdown",
	summary="Small description",
	version="0.1",
	terms_of_service="URL для условий пользования",
	contact={
		"name": "Name",
		"url": "URL",
		"email": "email",
	},
	license_info={
		"name": "license name",
		"url": "URL",
	}
)
```
- Конфигурация автоматической документации

```python
tags_metadata = [
	{
		"name": "...",
		"description": "...",
	},
	...
]

app FastAPI(openapi_tags=tags_metadata)
```
- Метаданные для тегов

```python
app = FastAPI(openapi_url="api/v1/openapi.json")
```
- По умолчанию схема OpenAPI находится по URL /openapi.json. Это можно изменить с помощью данной настройки. Чтобы удалить схему, используйте openapi_url=None

```python
app = FastAPI(docs_url="/document", redoc_url=None)
```
- Изменение конфигурации двух пользовательских интерфейсов документации (/docs и /redoc). В данном примере изменяется расположение Swagger UI и отключается ReDoc

## Статические файлы

```python
from fastapi import FastAPI
from fastapi.staticfiles import StaticFiles


app = FastAPI()

app.mount("/static", StaticFiles(directory="static"), name="static")
```
- Монтирование статических файлов

## Тестирование

Тестирование основано на httpx, которое основано на requests. Можно напрямую использовать pytest.

```python
from fastapi import FastAPI
from fastapi.testclient import TestClient


app = FastAPI()
client = TestClient(app)


def test_foo():
	response = client.get("/")
	assert response.status_code == 200
	assert response.json() = {}
```
- Тестирующая функция является синхронной. Это позволяет использовать pytest без усложнений