# FastAPI

- FastAPI - это современная, быстрый веб-фреймворк для создания API с помощью Python на основе аннотаций типов Python
- FastAPI основан на Scarlette и Pydantic
- Scarlette основан на AnyIO
- Высокая производительность, на уровне NodeJS и Go (благодаря Starlette и Pydantic)
- FastAPI генерирует `schema` со всеми API, используя стандарт OpenAPI для определения API
- Имеет встроенную автоматическую документацию от Swagger UI (/docs) и ReDoc (/redoc)
- **Schema** - это определение или описание чего-либо. Не код, который это реализует, а просто абстрактное описание
- **OpenAPI** - это спецификация, определяющая, как определить схему API
- **API schema** - пути API, возможные параметры, которые они принимают, и т. д.
- **Data schema** - атрибуты JSON, типы данных, которые они имеют, и т. д.
- OpenAPI определяет схему API. И эта схема включает определения (или schemas) данных, отправляемых и получаемых API с использованием **JSON schema**, стандарта для **JSON data schema**
- FastAPI автоматически генерирует JSON (необработанная схема OpenAPI) с описаниями всех API (доступно по /openapi.json). **OpenAPI schema** - это то, что лежит в основе двух включенных систем интерактивной документации
- **Путь** также часто называют **эндпоинтом** или **маршрутом**
- В OpenAPI HTTP методы называются операциями

## Installation

```bash
uv pip install fastapi[standard]
```
- Установка FastAPI с помощью `pip install fastapi[standard]` с некоторыми дополнительными стандартными зависимостями по умолчанию. Если вы не хотите иметь эти дополнительные зависимости, вы можете вместо этого установить `pip install fastapi`

## Run the live server

```bash
# Run fastapi in production mode
fastapi run

# Run fastapi in development mode
fastapi dev
```
- Запуск FastAPI

## Simple FastAPI

```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/items/{item_id}")
def read_item(item_id: int):
	return {"item_id": item_id}
```
- Значение параметра item_id будет передано в функцию, как аргумент item_id. Можно определить тип параметра в функции, как показано в примере. item_id является типом int
- Если перейти по пути /items/abc, то будет вызвана ошибка
- Если перейти по пути /items/1, то эндпоинт отработает корректно
- **Таким образом, с помощью обьявления типа Python FastAPI обеспечивает проверку данных**
- Вся проверка данных выполняется Pydantic

```python
from enum import Enum

from fastapi import FastAPI


class ModelName(str, Enum):
	aa = "aa"
	bb = "bb"
	cc = "cc"


app = FastAPI()


@app.get("/models/{model_name}")
async def get_model(model_name: ModelName):
	return {"model_name": model_name.value}
```
- Эндпоинт `get_model` получает предустановленные значения, в противном случае вызывает ошибку

```python

```
- 