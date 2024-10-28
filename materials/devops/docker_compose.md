# DOCKER COMPOSE

**Docker Compose** - это инструмент, который позволяет описывать и запускать на одном хосте группы контейнеров, которые зависят друг от друга. Он использует файл конфигурации в формате YAML для определения сервисов, сетей и томов, необходимых для приложения.

**ПРЕИМУЩЕСТВА**

- Позволяет легко определять и запускать несколько контейнеров как единое целое в рамках одного хоста.
- Автоматически создаёт сеть для контейнеров, определённых в файле `docker-compose.yaml`.
- Позволяет настраивать контейнеры с разными параметрами и зависимостями друг от друга.

Docker Compose подходит для разработки и тестирования приложений. Он позволяет быстро и легко создавать и запускать контейнеры с зависимостями. Это особенно полезно во время разработки приложений, состоящих из нескольких компонентов - БД, хранилища ключ-значение, менеджер очередей сообщений или отдельных веб-серверов.

Для небольших проектов, которые состоят из одного или нескольких микросервисов, когда не требуется сложная настройка сети или взаимодействие между контейнерами. Docker Compose предоставляет достаточную функциональность для управления контейнерами без излишней сложности.

Конфигурация Docker Compose описывается в YAML-манифесте. Обычно он называется `compose.yaml` или `docker-compose.yml`.

```yml
name: simple-python-app
services:
  web:
    image: nginx:alpine
    ports:
      - "80:80"
    networks:
      - frontend-net
    volumes:
      - type: bind
        source: ./nginx/app.conf
        target: /etc/nginx/conf.d/default.conf
        read_only: true
    depends_on:
      - simple_python_app

  simple_python_app:
    image: simple_python_app:${APP_VERSION:-latest}
    build: ./simple_python_app
    environment:
      API_DB_HOST: db
      API_DB_PASS: apipass
      API_DB_NAME: api
      API_DB_USER: apiuser
    networks:
      - frontend-net
      - backend-net
    depends_on:
      db:
        condition: service_healthy
        restart: true

  db:
    image: postgres:16.2-alpine
    environment:
      POSTGRES_PASSWORD: apipass
      POSTGRES_DB: api
      POSTGRES_USER: apiuser
    volumes:
      - dbdata:/var/lib/postgresql/data
    networks:
      - backend-net
    healthcheck:
      test: ["CMD-SHELL", "pg_isready"]
      interval: 10s
      timeout: 5s
      retries: 10
      start_period: 30s
      start_interval: 1s

networks:
  frontend-net:
  backend-net:

volumes:
  dbdata:
```
- Пример манифеста Docker Compose.
- Параметр `"80:80"` означает, что все сетевые запросы на порт хоста `80` будут переадресованы на порт `80` контейнера.
- Перед запуском сервисов Docker Compose создаёт тома, перечисленные в блоке `volumes`.
- Для сервиса `web` необходимо пробросить конфигурационный файл, чтобы Nginx переадресовывал все запросы на сервис `simple_python_app`. В нашем примере это также реализовано через параметр сервиса `volumes`.
- В блоке `networks` перечислены сети для связи сервисов. А в параметрах `networks` сервисов указаны сети, которые этими сервисами используются. При этом сеть `frontend-net` связывает только сервисы `web` и `simple_python_app`. А сеть `backend-net` связывает только сервисы `simple_python_app` и `db`.
- По умолчанию Docker Compose запускает все сервисы одновременно. Это может привести к ошибке. Например, если сервис `simple_python_app` будет запущен раньше сервиса `db`, то он упадёт с ошибкой невозможности подключения к базе данных. В примере указываем, что сервис `simple_python_app` зависит от сервиса `db`, а сервис `web` зависит от `simple_python_app`.
- Параметр `restart` указывает, что сервис `simple_python_app` будет перезапущен в случае обновления (и, как следствие. перезапуска) сервиса `db`.
- Параметр `condition` задаёт критерий ожидания. По умолчанию, в том числе при использовании короткого синтаксиса, применяется критерий `service_started`, то есть сервис будет запущен сразу после того, как будет запущен сервис, от которого он зависит. Мы же задали критерий `service_healthy`, означающий, что сервис `simple_python_app` будет запущен только после того, как сервис `db` будет «здоров».
- Критерий «здоровости» сервиса определяется параметром `healthcheck`.
- `test` Команда для проверки «здоровья» сервиса. В данном случае будет выполнена shell-команда `pg_isready`, которая отработает успешно после того, как postgresql сможет принимать соединения. Это произойдёт после инициализации базы данных: процесс может занять некоторое время.
- `interval: 10s` Означает, что после первой удачной проверки все последующие будут выполняться каждые 10 секунд.
- `timeout: 5s` Означает, что если в течение 5 секунд команда `pg_isready` не отработала, то попытка неудачная.
- `retries: 10` Означает, что после 10 неудачных проверок сервис перейдёт в статус «нездоров».
- `start_interval: 1s` Означает, что после запуска сервиса проверки будут выполняться каждую секунду, при этом в течение `start_period: 30s` неудачные попытки не будут учитываться. Однако после первой удачной проверки последующие будут выполняться с интервалом `interval: 10s`, и в случае неудачи количество попыток `retries: 10` будет учитываться.

> https://github.com/docker/awesome-compose

В блоке `services` описываются сами сервисы, запускаемые из образов контейнеров и их конфигурации: образ контейнера, передаваемые параметры запуска, переменные окружения, подключённые сети и точки монтирования. В качестве сервиса может выступать как контейнер на базе образа с вашим приложением, так и контейнер с веб-сервером, СУБД, брокером сообщений и подобными вспомогательными сервисами, необходимыми для работы приложения.

Сервисы взаимодействуют друг с другом посредством `networks`. В Docker Compose сеть описывает IP-маршрут между контейнерами со связанными сервисами. В блоке `networks` описываются сети и их параметры. Затем при описании каждого сервиса вы можете указать сети, к которым данный сервис подключён.

Сервисы сохраняют персистентные данные посредством `volumes`. Аналогично сетям, в блоке `volumes` описываются тома и их параметры. Затем для каждого сервиса, использующего том, этот том можно подключить.

Некоторым сервисам могут потребоваться `configs`, зависящие от среды исполнения или платформы. Для таких данных предусмотрен блок `configs`. С точки зрения контейнера, файл конфигурации не отличается от `volumes` - представляет собой файл, смонтированный в контейнер. Однако файл конфигурации предоставляет несколько другие возможности, например, вы можете задать содержимое смонтированного файла конфигурации прямо в его определении в `compose.yaml`.

Это способ передачи в контейнер чувствительных данных, которые не следует раскрывать по соображениям безопасности. Секреты аналогичны конфигам, однако взаимодействие с ними организовано с учётом чувствительности данных.

```bash
docker compose build
```
- Сборка или пересборка образов, хранилищ и сетевых конфигураций, определенных в yaml файле.

```bash
docker compose pull
```
- Установка зависимых образов на хост перед запуском `compose`, если они не установлены.

```bash
docker compose up

docker compose up -d --scale service_name=number_of_containers
```
- Создание и запуск контейнеров на основе образов, описанных в yaml файле. Флаг `--scale` позволяет указать количество контенеров сервиса.

```bash
docker compose ps
```
- Просмотр списка запущенных сервисов.

```bash
docker compose ls
```
- Список всех запущенных проектов на хосте с помощью compose.

```bash
docker compose logs web
```
- Вывод логов контейнера. Чтобы отслеживать логи в реальном времени, команде можно передать параметр `-f` или `--follow`.

```bash
docker compose events
```
- Отслеживание выполнения событий: healthchecks, запуск/перезапуск/остановка контейнеров, события OOM-killer в случае нехватки памяти и многие другие события.

```bash
docker compose run service_name command
```
- Запуск команды в сервисе.

```bash
docker compose down -v
```
- Остановить и удалить контейнеры и сети, определенные в yaml файле. Флаг `-v` позволяет удалить также тома, определенные в манифесте в блоке `volumes`.


```dockercompose
# Specifies the version of the Docker Compose file format.
version: ‘3.8’

# Defines the services/containers that make up the application
services:
	web:
		image: nginx:latest

# Configures custom networks for the application
networks:
	my_network:
		driver: bridge

# Defines named volumes that the services can use
volumes:
	my_volume:

# Sets environment variables for a service
environment:
	- NODE_ENV=production

# Maps host ports to container ports
ports:
	- “8080:80”

# Specifies dependencies between services, ensuring one service starts before another
depends_on:
	- db

# Configures the build context and Dockerfile for a service
build:
	context: .
	dockerfile: Dockerfile.dev

# Mounts volumes from another service or container
volumes_from:
	- service_name

# Overrides the default command specified in the Docker image.
command: [“npm”, “start”]
```
