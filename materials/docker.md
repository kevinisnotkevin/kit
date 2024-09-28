# Docker

Docker - open-source платформа для автоматизации развертывания приложений в виде автономных модулей, называемых контейнерами. Он использует многоуровневую файловую систему и функции изоляции ресурсов для обеспечения гибкости и переносимости.

Dockle - инструмент для проверки безопасности образов контейнеров, который можно использовать для поиска уязвимостей. Функции Dockle: поиск уязвимостей в образах, помощь в создании правильного Dockerfile.

Контейнеры Docker спроектированы так, чтобы быть неизменяемыми, это означает, что любые изменения, внесенные в контейнер во время выполнения, теряются при остановке контейнера. Поэтому рекомендуется использовать инструменты оркестрации контейнеров, такие как Docker Compose или Kubernetes, для управления контейнерами и их масштабирования в производственной среде.

## Компоненты

### Системные компоненты

Docker host (докер-хост) - это просто компьютер или виртуальный сервер, на котором установлен Docker. Кстати, Docker можно запустить и из WSL 2. Это удобно, если вы работаете под Windows и используете Visual Studio Code.

Docker daemon (докер-демон) - центральный системный компонент, который управляет всеми процессами докера: создание образов, запуск и остановка контейнеров, скачивание образов. Работает Docker daemon как фоновый процесс (демон) и постоянно следит за состоянием других компонентов.

Docker client (докер-клиент) - программа-интерфейс для командной строки, с которой взаимодействует пользователь. Он отдает команды через терминал. Клиент сообщает нужные сведения демону и отдает ему указания.

### Переменные компоненты

Docker Image (образ) - это шаблон. Например, образ может содержать операционку Ubuntu c Apache и приложением на ней. Образы используются для создания контейнеров. Docker позволяет легко создавать новые образы, обновлять существующие, или вы можете скачать образы созданные другими людьми. Его часто сравнивают со слоёным пирогом: мы накладываем слой файловой системы поверх слоя базового образа и получаем неизменяемый образ. В него можно установить приложение, конфигурации и зависимости. Другие образы могут наследоваться, поэтому если положить сверху слой файлов и закоммитить, то мы получим ещё один неизменяемый образ.

Образы хранятся в docker-реестре. Есть публичные и приватные реестры, из которых можно скачать либо загрузить образы. Публичный Docker-реестр - это Docker Hub. Там хранится огромная коллекция образов. Как вы знаете, образы могут быть созданы вами или вы можете использовать образы созданные другими. Реестры - это компонента распространения.

Контейнер - это runtime-сущность на основе образа, приложение, которое мы развернули с помощью Docker. Можно провести такую аналогию: образ - это инсталлятор программы, а контейнер - уже запущенная программа. В контейнерах содержится все, что нужно для работы приложения. Каждый контейнер создается из образа. Контейнеры могут быть созданы, запущены, остановлены, перенесены или удалены. Каждый контейнер изолирован и является безопасной платформой для приложения.

Каждый образ состоит из набора уровней. Docker использует union file system для сочетания этих уровней в один образ. Union file system позволяет файлам и директориями из разных файловых систем (разным ветвям) прозрачно накладываться, создавая когерентную файловую систему.

Одна из причин, по которой docker легковесен - это использование таких уровней. Когда вы изменяете образ, например, обновляете приложение, создается новый уровень. Так, без замены всего образа или его пересборки, как вам возможно придётся сделать с виртуальной машиной, только уровень добавляется или обновляется. И вам не нужно раздавать весь новый образ, раздается только обновление, что позволяет распространять образы проще и быстрее.

В основе каждого образа находится базовый образ. Например, ubuntu, базовый образ Ubuntu, или fedora, базовый образ дистрибутива Fedora. Так же вы можете использовать образы как базу для создания новых образов. Например, если у вас есть образ apache, вы можете использовать его как базовый образ для ваших веб-приложений.

Docker образы могут создаться из этих базовых образов, шаги описания для создания этих образов мы называем инструкциями. Каждая инструкция создает новый образ или уровень. Инструкциями будут следующие действия:

1. Запустить команду
2. Создать переменную окружения
3. добавить файл или директорию
4. указать, что запускать, когда запускается контейнер

## Разница между Docker и Docker Compose

Docker применяется для управления отдельными контейнерами (сервисами), из которых состоит приложение.

Docker Compose используется для одновременного управления несколькими контейнерами, входящими в состав приложения. Этот инструмент предлагает те же возможности, что и Docker, но позволяет работать с более сложными приложениями.

## Install

```bash
curl -sSL https://get.docker.com | sh
sudo usermod -aG docker $(whoami)
exit
```


## Searching an image

```bash
docker search nginx
docker search --filter stars=3 --no-trunc nginx
```


## Create Dockerfile

- Создадим Docker контейнер в качестве сервера «хостинга файлов» при передаче определенных файлов на целевые системы. Поэтому создадим Dockerfile на основе Ubuntu с работающим сервером Apache и SSH. Благодаря этому мы можем использовать scp для передачи файлов в образ Docker, а Apache позволяет нам размещать файлы и использовать такие инструменты, как Curl, wget и другие, в целевой системе для загрузки необходимых файлов.

```bash
FROM ubuntu:latest

RUN apt-get update && \
    apt-get install -y \
        apache2 \
        openssh-server \
        && \
    rm -rf /var/lib/apt/lists/*

RUN useradd -m docker-user && \
    echo "docker-user:password" | chpasswd

RUN chown -R docker-user:docker-user /var/www/html && \
    chown -R docker-user:docker-user /var/run/apache2 && \
    chown -R docker-user:docker-user /var/log/apache2 && \
    chown -R docker-user:docker-user /var/lock/apache2 && \
    usermod -aG sudo docker-user && \
    echo "docker-user ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers

EXPOSE 22 80

CMD service ssh start && /usr/sbin/apache2ctl -D FOREGROUND
```

После того, как _Dockerfile_ определен, нужно преобразовать его в _image_. С помощью команды _build_ берем каталог с _Dockerfile_, выполняем шаги из _Dockerfile_ и сохраняем _image_ в нашем локальном _Docker Engine_. Если один из шагов завершится неудачно из-за ошибки, создание контейнера будет прервано.

```bash
docker build .
```

После создания _image_ его можно выполнить с помощью _Docker Engine_, что делает его очень эффективным и простым способом запуска контейнера. _Image_ является шаблоном только для чтения и предоставляет файловую систему, необходимую для выполнения и всех параметров. Контейнер можно считать запущенным процессом образа. Когда контейнер должен быть запущен в системе, сначала загружается пакет с соответствующим образом, если он недоступен локально.

```bash
# Синтаксис Docker run
docker run -p <host port>:<docker port> -d <docker container name

# Создание образа
docker run -p 8022:22 -p 8080:80 image_name
```

Запущен новый контейнер из образа _image\_name_ и сопоставлены порты хоста 8022 и 8080 с портами контейнера 22 и 80 соответственно. Контейнер работает в фоновом режиме, что позволяет нам получать доступ к службам SSH и HTTP внутри контейнера, используя указанные порты хоста.


## Docker service

```bash
# Версия Docker
docker --version

# Очистка всех ресурсов – образы, контейнеры, volumes, networks
docker system prune

# Инициализация Docker внутри приложения
docker init

# Посмотреть сервис/контейнер приложения
docker compose watch
```


## Docker Image

**Docker-образ** – упаковка для приложения и зависимостей. Состоит из слоев. Каждый слой описывает какое-то изменение, которое должно быть выполнено с данными на запущенном контейнере. Структура связей между слоями – иерархическая. Имеется базовый слой,  на который накладываются остальные слои. Для создания образа используется Dockerfile. Каждая инструкция в нем создает новый слой.

```bash
# Создать образ из Dockerfile
docker build -t image_name path_to_dockerfile

# Список локальных образов
# Или: docker image ls
docker images

# Запулить образ из DockerHub
docker pull image_name:tag

# Удалить локальный образ
# Или: docker rmi container_id
docker rmi image_name:tag

# Тегнуть образ
docker tag source_image:tag new_image:tag

# Закинуть образ в DockerHub
docker push image_name:tag

# Детали образа
docker image inspect image_name:tag

# Сохранить образ в архив
docker save -o image_name.tar image_name:tag

# Загрузить образ из архива
docker load -i image_name.tar

# Удалить не используемые образы
docker image prune
```


## Docker container

```bash
# Запустить контейнер с именем из образа
docker run --name container_name image_name:tag

# Список всех запущенных контейнеров
docker ps

# Список всех контейнеров
docker ps -a

# Внутрь контейнера
docker exec -it container_name_or_id /bin/bash

# Статистика контейнера
docker stats container_name_or_id

# Остановить запущенный контейнер
docker stop container_name_or_id

# Запустить остановленный контейнер
docker start container_name_or_id

# Удалить остановленный контейнер
docker rm container_name_or_id

# Удалить запущенный контейнер
docker rm -f container_name_or_id

# Детали контейнера
docker inspect container_name_or_id

# Логи контейнера
docker logs container_name_or_id

# Поставить на паузу запущенный контейнер
docker pause container_name_or_id

# Поставить на плей запущенный контейнер
docker unpause container_name_or_id
```


## Docker volume

**Docker volume** – специальные директории на хостовой машине или удаленном сервере, которые монтируются в контейнер. Они обеспечивают постоянное хранение данных и могут использоваться для обмена данными между контейнерами.

```bash
# Create a named volume
docker volume create volume_name
# List of all volumes
docker volume ls
# Inspect details of a volume
docker volume inspect volume_name
# Remove a volume
docker volume rm volume_name
# Run a container with a volume (mount)
docker run --name container_name -v volume_name:/path/in/container image_name:tag
# Copy files between a container and a volume
docker cp local_file_or_directory container_name:/path/in/container
```


## Docker network

**Docker network (Port mapping)** – сеть, позволяющая контейнерам подключаться и взаимодействовать друг с другом. Сетевые драйверы:
- **bridge**: Сетевой драйвер по умолчанию;
- **host**: Удаляет сетевую изоляцию между контейнером и хостом;
- **overlay**: Оверлейные сети соединяют несколько демонов Docker вместе;
- **none**: Полностью изолирует контейнер от хоста и других контейнеров;
- **ipvlan**: Обеспечивают полный контроль над адресацией как IPv4, так и IPv6;
- **macvlan**: Назначает MAC-адрес контейнеру.

```bash
# Run a container with a port mapping
docker run --name container_name -p host_port:container_port image_name
# List all networks
docker network ls
# Inspect details of a network
docker network inspect network_name
# Create a user-defined bridge network
docker network create network_name
# Remove a network
docker network rm network_name
# Connect a container to a network
docker network connect network_name container_name
# Disconnect a container from a network
docker network disconnect network_name container_name
```


## Docker compose

Docker Compose - инструментальное средство, которое предназначено для решения задач, связанных с развертыванием проектов. Docker Compose может пригодиться, если для обеспечения функционирования проекта используется несколько сервисов. Используется для одновременного управления несколькими контейнерами, входящими в состав приложения.

```bash
# Create and start containers defined in a docker-compose.yml file. This command reads the docker-compose.yml file and starts the defined services in the background.
docker compose up

# Stop and remove containers defined in a docker-compose.yml file. This command stops and removes the containers, networks and volumes defined in the .yml file.
docker compose down

# Build or rebuild services. This command builds or rebuilds the Docker images for the services defined in the docker-compose.yml file.
docker compose build

# List containers for a specific Docker Compose project. This command lists the containers for the services defined in the docker-compose.yml file.
docker compose ps

# View logs for services. This command shows the logs for all services defined in the docker-compose.yml file.
docker compose logs

# Scale services to a specific number of containers
docker compose up -d --scale service_name=number_of_containers

# Run a one-time command in a service
docker compose run service_name command

# List all volumes. Docker Compose creates volumes for services. This command shows it
docker volume ls

# Pause a service. This command pauses the specified service
docker volume pause service_name

# Unpause a service
docker volume unpause service_name

# View details of a service
docker compose ps service_name
```


## Docker compose reference

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

## Dockerfile

```dockerfile
# Любой код или набор инструкций выполняется сверху вниз. Поэтому Dockerfile всегда начинается с открывающей инструкции FROM, которая говорит демону Docker, какой образ для основы нужно взять. Если образа локально нет - он будет скачан с Docker hub
FROM image_name:tag

# Есть инструкции логически связанные между собой. Инструкция WORKDIR устанавливает активный рабочий каталог. Все последующие команды, такие как COPY, RUN, CMD и некоторые другие будут выполнены из рабочего каталога, установленного через WORKDIR.
WORKDIR /path/to/directory

# Первым аргументом указывается папка для копирования, а вторым аргументом - папка в контейнере куда будут помещены файлы из копируемой директории
COPY host_source_path container_destination_path

# Далее идет инструкция RUN с определенной командой. Инструкций RUN может быть неограниченное количество, но стоит помнить, что каждая инструкция создает свой слой, поэтому хорошей практикой является запись цепочки команд через &&: RUN comand_1 && comand_2 && comand_3
RUN command1 && command2

# Sets environment variables in the image
ENV KEY=VALUE

# Informs Docker that the container listens on specified network ports at runtime
EXPOSE port

# Финальной инструкцией в любом Dockerfile является CMD или ENTRYPOINT. В отличие от других инструкций CMD может быть только одна и она может быть переопределена при старте контейнера командой docker run. Инструкция CMD наследует условия установленные инструкцией WORKDIR.
# Или: CMD executable param1 param2
CMD [“executable”, “param1”, “param2”]

# Configures a container that will run as an executable
# Или: ENTRYPOINT executable param1 param2
ENTRYPOINT [“executable”, “param1”, “param2”]

# Defines variables that users can pass at build-time to the builder with the docker build command
ARG VARIABLE_NAME=default_value

# Creates a mount point for external volumes or other containers
VOLUME /path/to/volume

# Adds metadata to an image in the form of key-value pairs
LABEL key=”value”

# Specifies the username or UID to use when running the image
USER user_name

# Copies files or directories and can extract tarballs in the process
ADD source_path destination_path

# Check a container's health on startup
HEALTHCHECK options

# Specify the author of an image
MAINTAINER name

# Specify instructions for when the image is used in a build
ONBUILD instruction

# Set the default shell of an image
SHELL [“executable”, “params”]

# Specify the system call signal for exiting a container
STOPSIGNAL signal
```


## Usage

### Docker + Django + PostgreSQL

Dockerfile

```dockerfile
FROM python:3.12.3-alpine3.19

COPY requirements.txt /temp/requirements.txt
COPY service /service
WORKDIR /service
EXPOSE 8000

RUN apk add postgresql-client build-base postgresql-dev
RUN pip install -r /temp/requirements.txt
RUN adduser --disabled-password service-user

USER service-user
```

Docker-compose.yml

```yml
services:
	web-app:
		build:
		context: .
		ports:
			- "8000:8000"
		volumes:
			- ./service:/service
		environment:
			- DB_HOST=database
			- DB_NAME=dbname
			- DB_USER=dbuser
			- DB_PASS=pwd
		command: >
			sh -c "python manage.py runserver 0.0.0.0:8000"
		depends_on:
			- database
	database:
		image: postgres:16.2-alpine
		environment:
			- POSTGRES_DB=dbname
			- POSTGRES_USER=dbuser
			- POSTGRES_PASSWORD=pwd
```

Далее переходим в каталог с докер файлами, создаем там директорию service/ и запускаем контейнер:

```bash
docker-compose build
docker-compose up
docker-compose run --rm sh -c "python manage.py migrate"
```

### MariaDB & Apache

```yml
version: '3'
services:
	web:
		build:
			context: ./html
			dockerfile: Dockerfile
		ports:
			- "8080:80"
		environment:
			- DATABASE_HOST=db
			- DATABASE_PORT=3306
			- DATABASE_NAME=first
			- DATABASE_USER=root
			- DATABASE_PASSWORD=1
		volumes:
			- ./html:/var/www/html
			- db_data:/var/lib/mysql
		depends_on:
			- db
		networks:
			custom_network:
				ipv4_address: 172.20.0.22
	db:
		image: mariadb
		environment:
			MYSQL_DATABASE: first
			MYSQL_USER: root
			MYSQL_PASSWORD: 1
			MYSQL_ROOT_PASSWORD: 1
		volumes:
			- db_data:/var/lib/mysql
		networks:
			custom_network:
				ipv4_address: 172.20.0.23

volumes:
	db_data: {}

networks:
	custom_network:
		driver: bridge
		ipam:
			config:
				- subnet: 172.20.0.0/16
```