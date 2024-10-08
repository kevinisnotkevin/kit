
# PYTHON-DOTENV

Файл `.env` - это текстовый файл, содержащий пары ключ-значение всех переменных окружения, требуемых вашим приложением. Этот файл включен в ваш проект локально, но не сохраняется в системе управления версиями, чтобы вы не подвергали риску потенциально конфиденциальную информацию.

Пример файла `.env`:

```python
DEBUG=True
TEMPLATE_DEBUG=True
SECRET_KEY=ARANDOMSECRETKEY
DATABASE_URL=mysql://myuser:mypassword@myhost/mydatabase
PERCENTILE=90%
```

Во всех языках есть модули которые работдают с файлами

Почти в каждом языке программирования есть пакет или библиотека, которые можно использовать для чтения переменных окружения из файла `.env`, а не из вашей локальной среды.

Библиотека **`Python-dotenv`** устанавливается стандартно:

```python
pip install python-dotenv
```

Как только библиотека установлена, обычный вариант использования `python-dotenv` требует добавления в ваш проект всего двух строк кода.

```python
import os
from dotenv import load_dotenv

load_dotenv()

SECRET_KEY= os.getenv('SECRET_KEY')
DATABASE_URL= os.getenv('DATABASE_URL')
```

По умолчанию `load_dotenv` будет искать файл `.env` в текущем рабочем каталоге или любых родительских каталогах, однако вы также можете указать путь, если ваш конкретный вариант использования требует, чтобы он хранился в другом месте.

```python
from dotenv import load_dotenv
from pathlib import Path

dotenv_path = Path('path/to/.env')
load_dotenv(dotenv_path=dotenv_path)
```

Изменим проект по подключению к удаленному хосту по ssh, используя переменные окружения.

Создадим файл `.env` со следующим содержимым:

```python
HOST = 192.168.133.145
PORT = 22
USER = user
PASSWORD = strongpassword
```

И изменим код проекта:

```python
import paramiko
import os

from dotenv import load_dotenv

host = os.getenv('HOST')
port = os.getenv('PORT')
username = os.getenv('USER')
password = os.getenv('PASSWORD')

client = paramiko.SSHClient()
client.set_missing_host_key_policy(paramiko.AutoAddPolicy())
client.connect(hostname=host, username=username, password=password, port=port)
stdin, stdout, stderr = client.exec_command('free -h')
data = stdout.read() + stderr.read()
client.close()
data = str(data).replace('\\\\n', '\\n').replace('\\\\t', '\\t')[2:-1]
print(data)
```

