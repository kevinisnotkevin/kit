# Pydantic

```python
from pydantic import BaseModel

from datetime import datetime


class User(BaseModel):
	id: int
	name: str = "John"
	signup_ts: datetime | None = None
	friends: list[int] = []

external_data = {
	"id": "123",
	"singup_ts": "2024-09-03 12:00",
	friends: [1, "2", b"3"],
}
user = User(**external_data)  # > User id=123 name='John' signup_ts=datetime.datetime(2024, 9, 3, 12, 00) friends=[1, 2, 3]
```