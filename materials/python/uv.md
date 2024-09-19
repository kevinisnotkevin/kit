# UV

uv - быстрый пакетный менеджер Python, написанный на rust. Заменяет pip, virtualenv, poetry и другие.

Сурс: https://github.com/astral-sh/uv

# Установка

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
powershell -c "irm https://astral.sh/uv/install.ps1 | iex"
pip install uv
```
- Разные споосбы установки uv

# Замена pip

```bash
uv python install 3.10 3.12
```
- Установка нескольких версий Python

```bash
uv venv
uv venv venv_name
uv venv --python 3.11
```
- Способы создания виртуального окружения

```bash
# Стандартная установка
uv pip install package_name

# Установка из github
uv pip install "git+https://github.com/astral-sh/ruff"

# Установка из файла
uv pip install -r requirements.txt
```
- Способы установки Python-пакетов

```bash
uv pip uninstall package_name
```
- Удаление модулей

```bash
# Листинг пакетов в requirements.txt
uv pip freeze > requirements.txt

# Листинг пакетов в JSON
uv pip list --format json
```
- Листинг установленных пакетов

