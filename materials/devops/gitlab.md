# GITLAB

**GitLab** - это веб-платформа для управления репозиториями Git, непрерывной интеграции и разработки (CI/CD), отслеживания ошибок, планирования задач. Он предоставляет командам разработчиков инструменты для совместной работы над проектами программного обеспечения.

```yml
services:
  gitlab:
    image: gitlab/gitlab-ce:17.5.1-ce.0
    container_name: gitlab
    restart: always
    hostname: "gitlab.example.ru"
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        external_url "https://gitlab.example.ru"
    ports:
      - "80:80"
      - "443:443"
      - "229:22"
    volumes:
      - "$GITLAB_HOME/config:/etc/gitlab"
      - "$GITLAB_HOME/logs:/var/log/gitlab"
      - "$GITLAB_HOME/data:/var/opt/gitlab"
    shm_size: "256m"
    env_file:
      - ".env"
```
- Установка Gitlab с помощью Docker compose.

```bash
sudo docker run --detach \ 
	--hostname gitlab.example.com \ 
	--env GITLAB_OMNIBUS_CONFIG="external_url 'http://gitlab.example.com'" \ 
	--publish 443:443 --publish 80:80 --publish 22:22 \ 
	--name gitlab \ --restart always \ 
	--volume $GITLAB_HOME/config:/etc/gitlab \ 
	--volume $GITLAB_HOME/logs:/var/log/gitlab \ 
	--volume $GITLAB_HOME/data:/var/opt/gitlab \ 
	--shm-size 256m \ gitlab/gitlab-ee:17.5.1-ce.0
```
- Установка gitlab с помощью docker engine.

```bash
sudo docker run --detach \ 
	--hostname gitlab.example.com \ 
	--env GITLAB_OMNIBUS_CONFIG="external_url 'http://gitlab.example.com'" \ 
	--publish 192.168.100.1:443:443 \ 
	--publish 192.168.100.1:80:80 \ 
	--publish 192.168.100.1:22:22 \ 
	--name gitlab \ 
	--restart always \ 
	--volume $GITLAB_HOME/config:/etc/gitlab \ 
	--volume $GITLAB_HOME/logs:/var/log/gitlab \ 
	--volume $GITLAB_HOME/data:/var/opt/gitlab \ 
	--shm-size 256m \ gitlab/gitlab-ee:17.5.1-ce.0
```
- Установка gitlab с помощью docker engine, используя IP, вместо домена.

```bash
cat /etc/gitlab/initial_root_password
```
- Файл с изначально сгенерированным паролем.

В `GITLAB_OMNIBUS_CONFIG` можно указывать настройки для gitlab.rb. Эти настройки не записываются в файл конфигурации gitlab.rb, но используются при загрузке.

```bash
vim docker exec -it gitlab vim /etc/gitlab/gitlab.rb
```
- Конфигурационный файл gitlab. В нем можно настроить, например:
- external_url: URL gitlab сервера.
- Параметры SMTP.
- Включить HTTPS.

```bash
gitlab-ctl reconfigure
# Или
docker restart gitlab
```
- После внесения изменений необходимо обновить конфигурацию.

```bash
docker exect -t gitlab gitlab-backup create
```
- Создание резервной копии gitlab.

```bash
docker exec -t gitlab gitlab-backup create SKIP=artifacts,repositories,registry,uploads,builds,pages,lfs,packages,terraform_state
```
- Создание резервной копии только БД. Полезно перед обновлением версии gitlab. Копия будет находиться в `/var/opt/gitlab/backups`.
