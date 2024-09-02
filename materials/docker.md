# Docker



- __Контейнеризация__ — процесс упаковки и запуска приложений в изолированных средах, таких как контейнер, виртуальная машина или бессерверная среда. Такие технологии, как Docker, Docker Compose и Linux Containers, делают этот процесс возможным в системах Linux. Эти технологии позволяют пользователям быстро, безопасно и эффективно создавать, развертывать приложения и управлять ими. С помощью этих инструментов можно настраивать приложения различными способами, что позволяет адаптировать приложение к потребностям. Кроме того, контейнеры невероятно легкие, идеально подходят для одновременного запуска нескольких приложений и обеспечивают масштабируемость и переносимость. Контейнеры изолированы от хост-системы и других контейнеров.
- __Docker__ – _open-source_ платформа для автоматизации развертывания приложений в виде автономных модулей, называемых контейнерами. Он использует многоуровневую файловую систему и функции изоляции ресурсов для обеспечения гибкости и переносимости.
- Предшественником Docker является виртуальная машина. Docker потребляет меньше ресурсов, его очень легко переносить и он быстрее запускается и приходит в работоспособное состояние.
- Для запуска Docker контейнера необходимы Docker engine и Docker images, которые можно получить из Docker Hub.
- Создание образа Docker осуществляется путем создания файла Dockerfile, который содержит все инструкции, необходимые движку Docker для создания контейнера.
- __Dockle__ – инструмент для проверки безопасности образов контейнеров, который можно использовать для поиска уязвимостей. Функции Dockle: поиск уязвимостей в образах, помощь в создании правильного Dockerfile.
- Контейнеры Docker спроектированы так, чтобы быть неизменяемыми, это означает, что любые изменения, внесенные в контейнер во время выполнения, теряются при остановке контейнера. Поэтому рекомендуется использовать инструменты оркестрации контейнеров, такие как Docker Compose или Kubernetes, для управления контейнерами и их масштабирования в производственной среде.


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

**Docker Compose** – инструментальное средство, которое предназначено для решения задач, связанных с развертыванием проектов. Docker Compose может пригодиться, если для обеспечения функционирования проекта используется несколько сервисов. Используется для одновременного управления несколькими контейнерами, входящими в состав приложения.

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


## Dockerfile reference

**Dockerfile** – текстовый документ, содержащий все команды, которые пользователь может вызвать в командной строке для сборки образа. Ниже перечислены поддерживаемые инструкции Dockerfile.

```dockerfile
# Specifies the base image for the Docker image
FROM image_name:tag
# Sets the working directory for subsequent instructions
WORKDIR /path/to/directory
# Copies files or directories from the build context to the container
COPY host_source_path container_destination_path
# Executes commands in the shell
RUN command1 && command2
# Sets environment variables in the image
ENV KEY=VALUE
# Informs Docker that the container listens on specified network ports at runtime
EXPOSE port
# Provides default commands or parameters for an executing container
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
