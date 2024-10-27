# PostgreSQL

**PostgreSQL** - это открытая реляционная система управления базами данных (СУБД), разработанная для работы с большими объемами данных и высокими нагрузками. Она поддерживает SQL для управления данными и широко используется для построения как небольших, так и масштабируемых систем.

Основные особенности PostgreSQL:
- **Открытый исходный код**: PostgreSQL распространяется бесплатно, с открытым исходным кодом и активно развивается сообществом разработчиков.
- **Поддержка стандарта SQL**: PostgreSQL соответствует стандарту SQL и предоставляет расширенные функции для работы с транзакциями, индексами, триггерами и хранимыми процедурами.
- **Расширяемость**: СУБД поддерживает пользовательские типы данных, функции, языки процедур и расширения, что делает её гибкой и адаптируемой для различных сценариев.
- **Поддержка ACID**: PostgreSQL гарантирует целостность данных, поддерживая свойства ACID (атомарность, согласованность, изолированность, долговечность).
- **Масштабируемость**: Подходит как для небольших приложений, так и для крупных распределённых систем, обрабатывающих большие объёмы данных.
- **Поддержка расширенных типов данных**: Помимо стандартных типов данных (строки, числа и т.д.), PostgreSQL поддерживает JSON, XML, массивы и географические данные (через PostGIS).
- **Многопользовательская среда**: PostgreSQL позволяет создавать пользователей и управлять их правами доступа к базам данных и таблицам.

## Установка

```bash
apt update && apt install postgresql postgresql-contrib
```
- Установка PostgreSQL

## Создание БД

```bash
# Создание БД
CREATE DATABASE dbname;

# Создание пользователя
CREATE USER dbuser WITH PASSWORD 'password';

# Назначение прав пользователю на БД
GRANT ALL PRIVILEGES ON DATABASE dbname TO dbuser;

# Удаление пользователя
DROP ROLE dbuser;
```

## PSQL

**psql** - это cli для работы с БД postgresql. С помощью psql можно подключаться к серверу postgresql, выполнять sql-запросы, управлять БД, таблицами, пользователями и получать информацию о состоянии системы.

```bash
sudo -u postgres psql
```
- Подключение к postgresql

```bash
psql -U username -d dbname -h host -p 5432
```
- Подключение к postgresql

```bash
# Выйти из psql
\q

# Показать список БД
\l

# Подключиться к БД
\c dbname

# Показать список таблиц
\dt

# Показать список пользователей
\du

# Показать структуру таблицы
\d tablename

# Список индексов
\di

# Список таблиц всех схем
\dt *.*
```
- Подключение и навигация

```
pg_dump dbname > backup.sql
```
- Создание резервной копии БД

```
psql dbname < backup.sql
```
- Восстановление БД из бэкапа

## PostgreSQL & Docker

```bash
docker run -p 5432:5432 --name postgres -e POSTGRES_PASSWORD=1 POSTGRES_USER=username POSTGRES_DB=dbname -v "/path/to/dir/with_init_scripts":/docker-entrypoint-initdb.d  -e PG_DATA=/var/lib/postgresql/data/pgdata -v "/path/to/dir_with_data":/var/lib/postgresql/data -d postgres 
```
- Создание контейнера с PostgreSQL. Пробрасываем порт на 5432. Даем название контейнеру postgres. Указываем переменные. Указываем путь директории в которой находятся скрипты для инициализации БД. С помощью PG_DATA примонтируем каталог в который будут сохраняться данные на хосте из контейнера. Указываем образ.

```yaml
version: "3"
services:
  postgres:
    image: postgres
    environment:
      POSTGRES_DB: "dbname"
      POSTGRES_USER: "username"
      POSTGRES_PASSWORD: "1"
      PGDATA: "/var/lib/postgresql/data/pgdata"
    ports:
      - "5432:5432"
    volumes:
      - .:/docker-entrypoint-initdb.d
      - harddb-data:/var/lib/postgresql/data

volumes:
  harddb-data:
```
- Аналогичная запись, но в docker compose. Здесь есть возможность указать относительный путь до директории со скриптами инициализации БД (Указана точка)

### Healthcheck

Healthcheck - Проверка работоспособности. Основная задача healthcheck - как можно скорее уведомить среду, управляющую контейнером, о том, что с контейнером что-то не так. И самая простая стратегия решения проблемы - перезапуск контейнера.

Healthcheck для PostgreSQL обычно основывается на использовании утилиты pg_isready.

```yaml
version: "3"
services:
  db:
    ...
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U username -d dbname"]
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 10s
    restart: unless-stopped
    deploy:
      resources:
        limits:
	      cpus: "1"
	      memory: 4G
```
- Настраиваем healthcheck и ограничиваем ресурсы для контейнера

### Настройка параметров БД

```yaml
version: "3"
services:
  db:
    ...
    command:
      - "postgres"
      - "-c"
      - "max_connections=50"
      - "-c"
      - "shared_buffers=1GB"
      - "-c"
      - "effective_cache_size=4GB"
      - "-c"
      - "work_mem=16MB"
      - "-c"
      - "maintenance_work_mem=512MB"
      - "-c"
      - "random_page_cost=1.1"
      - "-c"
      - "temp_file_limit=10GB"
      - "-c"
      - "log_min_duration_statement=200ms"
      - "-c"
      - "idle_in_transaction_session_timeout=10s"
      - "-c"
      - "lock_timeout=1s"
      - "-c"
      - "statement_timeout=60s"
      - "-c"
      - "shared_preload_libraries=pg_stat_statements"
      - "-c"
      - "pg_stat_statements.max=10000"
      - "-c"
      - "pg_stat_statements.track=all"
```
- Настройка параметров БД при создании контейнера

### pgAdmin

```yaml
version: "3"
services:
  db:
    ...
    networks:
      postgres
  pgadmin:
    container_name: pgadmin
    image: dpage/pgadmin4
    environment:
      PGADMIN_DEFAULT_EMAIL: "example@example.com"
      PGADMIN_DEFAULT_PASSWORD: "1"
      PGADMIN_CONFIG_SERVER_MODE: "False"
    volumes:
      - pgadmin-data:/var/lib/pgadmin
    ports:
      - "5050:80"
    restart:
      unless-stopped
    deploy:
      resources:
        limits:
          cpus: "0.5"
          memory: 1G
    networks:
      - postgres

volumes:
  pgadmin-data:

networks:
  postgres:
    driver: bridge
```
- Подключение pgAdmin на 5050 порт
