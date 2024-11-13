# TRAEFIK

**Traefik** - это прокси-сервер с открытым исходным кодом, который включает динамическую настройку, автоматическое обнаружение служб, поддержку серверных частей и протоколов.

**ВОЗМОЖНОСТИ**

- Автоматическое обнаружение контейнеров и обновление правил маршрутизации при изменении инфраструктуры.
- Балансировка нагрузки по доменам или url.
- Автоматическое получение и обновление ssl сертификатов от lets encrypt.
- Поддержка prometheus и grafana.

Traefik перехватывает и маршрутизирует каждый входящий запрос. При развертывании сервиса, необходимо сообщить traefik характеристики запросов, которые сервис может обрабатывать. При развертывании или удалении службы Traefik автоматически обнаруживает ее и обновляет правила маршрутизации.

При запуске traefik ищет `traefik.yml` файл конфигурации в `/etc/traefik/`, `$XDG_CONFIG_HOME/`, `$HOME/.config/`, `.`. Указать путь к конфигу можно с помощью флага `traefik --configFile=/path/to`.

## Компоненты

**Статическая конфигурация** - это конфигурация, которая задается при запуске и включает параметры запуска (порты, точки входа, провайдеры).

Статическая конфигурация может быть определена в файле конфигурации или в аргументах командной строки или в переменных среды. Если не указывать значения для параметров, то используются значения по умолчанию.

**Динамическая конфигурация** - это конфигурация, которая описывает маршруты, правила, middleware и меняется без перезапуска.

Traefik получает динамическую конфигурацию от провайдеров (оркестратор, реестр служб, файл конфигурации).

**Компоненты:**

- **Providers**: Обнаруживают сервисы в инфраструктуре (ip, работоспособность)
- **Entrypoints**: Слушают входящий трафик (порты)
- **Routers**: Анализируют запросы (host, path, headers, ssl)
- **Services**: Перенаправляют запрос сервисам (load balancing)
- **Middlewares**: Обновляют запрос (auth, rate limiting, headers)

### Providers

**Providers** - это компоненты инфраструктуры (Docker, Kubernetes и др), которые предоставляют API-интерфейсы, через которые traefik получает информацию о сервисах, которые должны быть маршрутизированы.

Обьекты в динамической конфигурации (middleware, services, tls-params) находятся в пространстве имен своего провайдера, но могут быть использованы в динамической конфигурации другого провайдера.

Источники могут быть разных категорий. Категории определяют то, каким образом traefik получает информацию о сервисах, маршрутах и правилах маршрутизации. 

Категории:

- **Labels**: Контейнеры содержат набор лейблов с конфигурациями, которые traefik будет использовать для маршрутизации.
- **Key-Value**: Контейнеры обновляют специальное хранилище данных в формате ключ-значение (Например, consul, etcd) инфомацией, связанной с их состоянием и конфигурацией.
- **Annotations**: Kubernetes-поды или сервисы содержат аннотации, которые описывают, как именно traefik должен маршрутизировать трафик к этим обьектам.
- **Files**: Этот подход предполагает использование файлов конфигураций для описания параметров маршрутизации и сервисов. Файлы могут быть статические (`traefik.yml`) или динамические.

```yml
providers:
  docker: {}

# Или
providers:
  docker:
    endpoint: "unix:///var/run/docker.sock"  # По умолчанию
```
- Пример подключения Docker в качестве провайдера traefik. Docker использует лейблы контейнеров для получения конфигурации маршрутизации.

```yml
services:
  cont:
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
```
- Далее, необходимо дать доступ traefik к docker сокету, чтобы он мог мониторить контейнеры.

```yml
services:
  cont:
    labels:
      - traefik.http.routers.cont.rule=Host(`example.com`)
```
- После подключения провайдера прикрепляем лейблы к контейнерам.
- Теперь traefik обнаружит контейнеры с лейблами и динамически обновит конфигурацию маршрутизации.

```yml
providers:
  providersThrottleDuration: 10s
```
- Параметр устанавливает продолжительность, в течение которой traefik ожидает после перезагрузки конфигурации, прежде чем принимать во внимание любое новое событие конфигурации. Если в течение этого времени происходит несколько событий, то учитывается только последнее.

```yml
providers:
  docker:
    # Использовать ip адрес, привязанный к порту контейнера
    # Полезно в host, macvlan сетях, где у контейнера может быть несколько сетевых интерфейсов
    useBindPortIP: false
    # Использовать все обнаруженные контейнеры (true) или только те, у которых есть лейбл traefik.enable=true (false)
    exposedByDefault: true
    # Определяет в какой docker сети искать контейнеры
    network: ""
    # Тайм-аут в секундах клиента для http соединений
    httpClientTimeout: 0
```
- Пример провайдера, который должен отслеживать docker контейнеры.

### Entrypoints

**Entrypoints** - это конфигурации, которые определяют, на каких портах и протоколах traefik будет слушать входящий трафик.

При запуске traefik определяются точки входа (номера портов). Подключаясь к этим точкам входа, маршрутизаторы анализируют входящие запросы на соответствие установленным правилам (`Rules`). При нахождении соответствия, маршрутизатор преобразует запрос, используя установленные мидлвари, затем запрос перенаправляется сервис.

```yml
entryPoints:
  entrypoint_name:
    address: [host]:port/[tcp|udp]  # Адрес и порт для прослушивания входящих соединений и пакетов. По умолчанию tcp протокол
    allowACMEByPass: false  # Пропуск проверки ACME
    asDefault: false  # Установить точку входа по умолчанию для tcp и http
    http2:
      maxConcurrentStreams: 250  # Кол-во одновременных потоков на соединение
    http3: {}  # Включение http3
    forwardedHeaders:  # Настройка доверия для X-Forwarded-* заголовков
      trustedIPs:  # Доверие загловкам от ip адресов или cidr диапазонов
        - "127.0.0.1/32"
      insecure: true  # Всегда доверять заголовкам
    transport:
      respondingTimeouts:  # Тайм-ауты для входящих запросов, кроме udp
        readTimeout: 60s  # Тайм-аут чтения всего запроса перед ответом
        writeTimeout: 0s  # Тайм-аут отправки ответа
        idleTimeout: 180s  # Тайм-аут бездействия keep-alive соединения
      lifeCycle:  # Управление поведением перед завершением
      keepAliveMaxRequests: 0s  # Кол-во обработанных запросов перед закрытием соединения
      keepAliveMaxTime: 0s  # Продолжительность работы перед закрытием соединения

    http:  # Параметры для http трафика
      redirections:
        entryPoint:  # Редирект запросов с текущей точки входа на указанную
          to: https  # Целевая точка входа (имя или порт)
          scheme: https  # Схема перенаправления (default)
          permanent: true  # Постоянное перенаправление 301 статус иначе 302 (default)
      middlewares:  # Подключение middleware
      tls:  # Аналогично tls на http Routers
    udp:  # Параметры udp роутинга
      timeout: 3s  # Тайм-аут ожидания перед освобождением ресурсов
```
- Параметры точки входа.

```yml
entryPoints:
  web:
    address: ":80"
  websecure:
    address: ":443"
  streaming:
    address: ":1704/udp"
```
- Определение точек входа.
- Точка входа `web` слушает 80 порт tcp.
- Точка входа `websecure` слушает порт 443 tcp.
- Точка входа `streaming` слушает порт 1704 udp.

### Routers

**Routers** - это компоненты, которые определяют, какие запросы будут обработаны и какой сервис их обслужит.

Чтобы обновить конфигурацию роутера, автоматически прикрепленного к контейнеру, необходимо добавить лейблы, начинающиеся с `traefik.http.routers.<router_name>`, а затем параметр, который нужно изменить.

В docker, лейблы контейнеров позволяют настраивать правила маршрутизации. traefik создает для каждого контейнера сервис и роутер. Сервис автоматически получает сервер для каждого контейнера, а роутер автоматически получает правило `defaultRule`, если у контейнера нет лейбла `rule`.

Если лейбл определяет роутер (Например, с помощью `rule`) и метка определяет сервис (Например, через `loadBalancer.server.port`), но роутер не указывает на сервис, то этот сервис автоматически назначается роутеру.

Или, если лейбл определяет роутер (Например, с помощью `rule`), но сервис не определен, то сервис автоматически создается и назначается роутеру.

```yml
"traefik.http.routers.myproxy.rule=Host(`example.com`)"
```
- Параметр определяет, что запросы по доменному имени `example.com` будут направляться в сервис `app`.
- Критерии могут быть на основе: `header`, `host`, `method`, `path`, `query`, `clientip`. Если правило проверено, то роутер становится активным и вызывает мидлварь, а затем перенаправляет запрос в контейнер.
- Значение правила можно указывать, либо с помощью обратного апострофа, либо с помощью двойных кавычек, экранированных с помощью обратного слеша.

```yml
"traefik.http.routers.myproxy.service=myservice"
"traefik.http.routers.myproxy.service=api@internal"
```
- Параметр явно определяет сервис для роутера, который обработает запрос. Имя сервиса не зависит от имени сервиса docker compose и может быть любым.
- `api@internal`: Указывает на api traefik, которое предоставляет доступ к информации о текущих маршрутах, подключенных сервисах и др. Нужно для доступа к панели traefik.

```yml
"traefik.http.routers.myproxy.entrypoints=ep1,ep2"
```
- Параметр определяет список точек входа.
- По умолчанию прослушиваются все точки.

```yml
"traefik.http.routers.myproxy.priority=2"
```
- Параметр определяет приоритет роутера.
- Если несколько роутеров могут обработать один запрос, то запрос направляется роутеру с наивысшим приоритетом. По умолчанию приоритет равен длине правила, т.е. чем больше длина правила, тем выше приоритет роутера.

```yml
"traefik.http.routers.myproxy.middlewares=m1,m2"
```
- Параметр определяет список мидлварь, которые отработают перед пересылкой запроса серверу, если выполнится правило. Мидлвари выполняются в порядке, обьявленном в списке.

```yml
"traefik.http.routers.myproxy.tls=true"
```
- Параметр сообщает, что текущий маршрутизатор предназначен только для https соединения. ssl соединения будут прекращены.

```yml
"traefik.http.routers.myproxy.tls.certResolver=myresolver"
```
- Параметр определяет разрешитель сертификатов, который отвечает за автоматическое получение и обновление сертификатов на основе правила `Host` роутера. Это позволяет интегрироваться с провайдерами, например, lets encrypt.

```yml
"traefik.http.routers.myproxy.tls.options=foobar"
```
- Параметр описывает специфические настройки tls (протоколы, версии, шифры).

```yml
"traefik.http.router.myproxy.tls.domains[0].main=example.com"
"traefik.http.router.myproxy.tls.domains[0].sans=sans.example.com"
```
- Параметры определяют список доменов для роутера, которым нужно получить сертификаты. Если один сервис обслуживает несколько доменов или поддоменов, то traefik позволяет получить один сертификат для всех этих доменов, а не создавать отдельные роутеры для каждого домена.

### Services

**Services** - это компоненты, которые отвечают за настройку способа доступа к фактическим сервисам, которые в конечном итоге будут обрабатывать входящие запросы. 

traefik получает ip адрес и порты контейнеров из docker api. Если у контейнера открыт один порт, то он и используется. Если открыто несколько портов, то используется наименьший. Если у контейнера не открыты порты, то необходимо вручную указать, какой порт traefik использовать для связи.

Чтобы обновить конфигурацию сервиса, автоматически прикрепленной к контейнеру docker, необходимо добавить лейблы, начинающиеся с `traefik.http.services.<service_name>`, а затем параметр, который нужно изменить.

Балансировщики нагрузки балансируют запросы между несколькими экземплярами ПО. Каждый сервис имеет балансировщик нагрузки, даже если есть только один сервер, который принимает трафик.

```yml
"traefik.http.services.myservice.loadbalancer.server.port=123"
```
- Параметр определяет, используемый порт контейнера. Такая запись автоматически назначает сервис `myservice` роутеру.
- Полезно, когда контейнер предоставляет несколько портов.
- Если не указать сервис, то по умолчанию используется первый открытый порт контейнера и сервис создается автоматически и назначается роутеру, который описан ранее.

```yml
"traefik.http.services.myservice.loadbalancer.server.scheme=http"  # (default)
```
- Параметр определяет какой протокол использовать для связи с конечным сервисом (контейнером).
- Полезно, если трафик на входе https, а контейнер слушает только http.

```yml
"traefik.http.services.myservice.loadbalancer.passhostheader=true"  # (default)
```
- Параметр определяет, пересылать ли `Host` заголовок хоста клиента на сервер.

```yml
"traefik.http.services.myservice.loadbalancer.healthcheck..."
```
- Параметры настраивают healthcheck для проверки работоспособности сервера, чтобы направлять трафик только на исправные серверы.

```yml
"traefik.http.services.myservice.loadbalancer.sticky..."
```
- Параметры определяют, будет ли клиент в течение сессии оставаться на одном сервере.

### Middleware

**Middleware** - это компоненты, которые обрабатывают запросы между `Routers` и `Services`.

В docker, чтобы обьявить middleware, используется лейбл `traefik.http.middlewares.<custom_name>`, за которым следует тип/опции.

```yml
"traefik.http.middlewares.mymiddleware.redirectscheme.scheme=https"
```
- Пример мидлварь, которая редиректит трафик.

Позволяет добавлять дополнительные функции, например, аутентификацию, ограничение доступа, редиректы, добавление заголовков.

```yml
http:
  middlewares:
    redirect-to-https:
      redirectScheme:
        scheme: https
        permanent: true

  routers:
    app:
      rule: "Host(`app.local`)"
      entryPoints:
        - web
      service: app-service
      middlewares:
        - redirect-to-https
```
- Пример middleware `redirect-to-https`, которая перенаправляет запросы `app.local` с 80 порта на 443.

## Глобальная настройка соединений с сервисами

```yml
serversTransport:
  insecureSkipVerify: false  # Отключает проверку ssl сертификата
  maxIdleConnsPerHost: 2  # Макс кол-во бездействующих keep-alive соединений на каждом хосте
  forwardingTimeouts:  # Тайм-ауты для передачи данных на внутренние сервисы
    dialTimeout: 30s  # Тайм-аут для установления соединения с сервисом
    responseHeaderTimeout: 0s  # Тайм-аут ожидания заголовков ответа сервиса
    idleConnTimeout: 90s  # Тайм-аут бездействующего keep-alive соединения
tcpServersTransport:
  dialTimeout: 30s  # Тайм-аут установки соединения с сервером
  dialKeepAlive: 15s  # Интервал между проверками активных сетевых соединений
  tls:  # Определяет конфигурацию tls для соединения с серверами tcp. Значение {} разрешает tls
```
- Статические параметры транспортного уровня для соединений между traefik и сервисами.
- `serversTransport` предназначен для http сервисов.
- `tcpServersTransport` предназначен для tcp сервисов.

## Certificate resolvers

В статической конфигурации необходимо определить resolvers, которые отвечают за получение сертификатов с сервера acme. Далее, каждый роутер настраивается на включение tls и связывается с определенными resolvers с помощью параметра `tls.certResolvers`. Если на роутере установлен параметр `tls.domains`, то resolver берет имя домена из `main` и `sans`. Иначе, имя домена будет взято из `Host()` правила роутера.

traefik отслеживает дату истечения срока действия сертификатов acme, которые генерирует. По умолчанию сертификаты генерируются на 90 дней и продлеваются за 30 дней до истечения срока действия.

```yml
certificatesResolvers:
  myresolver:
    acme:
      email: "test@example.com"
      storage: "acme.json"  # (default)
      caServer: "https://acme-v02.api.letsencrypt.org/directory"  # (default)
      certificatesDuration: 2160  # 90 дней (default)
      keyType: RSA4096  # (default)
      tlsChallenge: {}
```
- Пример конфигурации acme.
- `email`: Почта для регистрации.
- `storage`: Место хранения acme сертификатов. Файл должен иметь права доступа 600.
- `caServer`: Сервер CA, который будет использоваться. Let's Encrypt имеет ограничения по выдаче сертификатов, на время разработки рекомендуется использовать `https://acme-staging-v02.api.letsencrypt.org/directory`.
- `certificatesDuration`: Длительность сертификата в часах.
- `keyType`: Тип ключа (EC256, EC384, RSA2048, RSA4096, RSA8192).



```yml
services:
  traefik:
    image: traefik:v3.2
    container_name: traefik
    command: 
      - "--api.insecure=true"
      - "--providers.docker=true"
      - "--providers.docker.exposedByDefault=false"
      - "--entrypoints.websecure.address=:443"
      - "--certificatesresolvers.myresolver.acme.tlschallenge=true"
      - "--certificatesresolvers.myresoler.acme.email=example@mail.com"
      - "--certificatesresolvers.myresolver.acme.storage=/letsencrypt/acme.json"
    ports:
      - "443:443"
      - "8080:8080"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock  # Доступ к сокету docker
      - ./letsencrypt:/letsencrypt  # Директория для хранения сертификатов

  nginx1:
    image: nginx
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.nginx.entrypoints=websecure"
      - "traefik.http.routers.nginx.tls.certresolver=myresolver"
      - "traefik.http.routers.nginx.rule=Host(`example.com`)"
```


- `--entrypoints`: Определяет точки входа для маршрутизации трафика.
- `--certificatesresolvers`: Определяет процесс получения и обновления сертификатов.

```yml
"--entryPoints.websecure.address=:443"
```
- Указание точки входа с именем `websecure` прослушивать соединения на 443 порту. 
- `websecure` - произвольное имя. 
- `:443` - означает, что точка входа будет принимать запросы на 443 на всех доступных IP-адресах. 

```yml
"--certificatesresolvers.myresolver.acme.tlschallenge=true"
```
- Указание использования TLS Challenge для получения SSL-сертификатов по протоколу ACME (Например, Lets Enctypt). Это позволяет автоматически получать сертификаты для домена, используя процесс проверки через TLS. 
- `myresolver` - произвольное имя.

```yml
"--certificatesresolvers.myresolver.acme.storage=/letsencrypt/acme.json"
```
- Указание места хранения сертификатов.

```yml
"--traefik.http.routers.nginx.tls.certsolver=myresolver"
```
- Указание сервису nginx использовать преобразователь сертификатов, который был выше настроен.

## Docker

```yml
services:
  reverse_proxy:
    image: traefik:v3.2
    command: 
      - "--api.insecure=true"  # Включение web-ui
      - "--providers.docker"  # Прослушивание docker
      - "--log.level=DEBUG"
      - "--providers.docker.exposebydefault=false"  # Не использовать контейнер, если об этом явно не указано
    ports:
      - "80:80"
      - "8080:8080"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro  # Доступ к сокету docker
```
- Создание экземпляра traefik с аргументами CLI.

```yml
services:
  reverse_proxy:
    image: traefik:v3.2
    ports:
      - "80:80"
      - "8080:8080"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro
      - ./traefik.yml:/traefik.yml:ro
```
- Создание экземпляра traefik с файлом конфигураций.

```yml
services:
  whoami:
    image: traefik/whoami
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.whoami.rule=Host(`whoami.localhost`)"  # Домен, на который будет отвечать сервис
```
- Создание сервиса, который выводит информацию о хосте, на котором развернут traefik. Traefik автоматически обнаружит новый контейнер и обновит конфигурацию.

```bash
services:
  nginx1:
    image: nginx
    labels:
      - "traefik.http.routers.nginx.rule=Host(`test.example`)"
```
- Создание веб-сервера и подключение к traefik.


```yml
traefik.enable=true
```
- Указывает, что нужно отслеживать этот контейнер и использовать его для маршрутизации трафика.


## http -> https

Есть несколько способов настроить перенаправление трафика.

```yml
"traefik.http.routers.myapp.rule=Host(`example.com`)"
"traefik.http.routers.myapp.entrypoints=web"  # HTTP
"traefik.http.routers.myapp.middlewares=redirect-to-https"
"traefik.http.middlewares.redirect-to-https.redirectscheme.scheme=https"  # Редирект на HTTPS
```
- Первый способ. В этом примере трафик будет автоматически перенаправляться с HTTP на HTTPS для сервиса `myapp`.

```yml
"traefik.http.routers.myapp.rule=Host(`example.com`)"
"traefik.http.routers.myapp.entrypoints=web" # HTTP
"traefik.http.routers.myapp.middlewares=redirect-to-https" # Применяем middleware
"traefik.http.middlewares.redirect-to-https.redirect.permanent=true" # Постоянное перенаправление
"traefik.http.middlewares.redirect-to-https.redirect.to=https://example.com" # Конечный URL
```
- Второй способ. Редирект с помощью `redirect` middleware, которое предлагает больше гибкости, чем `redirectscheme`.

```yml
# Статическая конфигурация
"--entrypoints.web.address=:80" # HTTP
"--entrypoints.websecure.address=:443" # HTTPS
"--entrypoints.web.http.redirections.entryPoint.to=websecure" # Редирект с HTTP на HTTPS
"--entrypoints.web.http.redirections.entryPoint.scheme=https" # Указывает использовать HTTPS
"--entrypoints.web.http.redirections.entryPoint.permanent=true"  # Постоянные редирект

# Динамическая конфигурация
- "traefik.http.routers.myapp.rule=Host(`example.com`)"
- "traefik.http.routers.myapp.entrypoints=websecure" # Роутер для HTTPS
```
- Третий способ. `entrypoints` автоматически перенаправит трафик с HTTP на HTTPS для всех маршрутов, которые подключены к HTTP.

## Получить хеш пароля

```bash
echo $(htpasswd -nB username) | sed -e s/\\$/\\$\\$/g
```
- Получение хеша для basicAuth

## Dashboard

Настройка дашборда traefik.

1. Включить `dashboard` в `api`.
2. Подключить роутер traefik к сервису `api@internal`. Правило роутера должно соответствовать префиксам пути `/dashboard` и `/api`. Лучше использовать правило на основе хоста.

Другой способ (Небезопасно):

1. Включить `dashboard` и `insecure` в `api`.
2. Подключиться к 8080 порту.

## CLI

```bash
traefik healthcheck
```
- Вызывает `/ping` для проверки работоспособности traefik. Должен быть включен api.
- Можно использовать с `docker healthcheck`.

## API

```yml
api:
  dashboard: true  # (default)
  insecure: true
  debug: false  # (default)
```
- `api`: Предоставляет конфигурации всех роутеров, сервисов и др. Не рекомендуется открывать api публично, лучше ограничить ее внутренней сетью. При включении создается сервис `api@internal`.
- `dashboard`: Включение панели мониторинга.
- `insecure`: Включает незащищенный режим api (api будет доступен в точке входа). Если точка входа `traefik` не настроена, то она будет автоматически создана на 8080 порту.
- `debug`: Включение доп. эндпоинтов `/debug/` для отладки.

## Logs

По умолчанию логи пишутся на стандартный вывод в текстовом формате.

```yml
log:
  filePath: "/var/log/traefik/traefik.log"
  format: common  # (default)
  level: ERROR  # (default)
  nocolor: true
  maxSize: 100  # (default)
  maxBackups: 3
  maxAge: 3
  compress: false  # (default)
```
- Конфигурация журналов событий.
- `filePath`: Путь к файлу для записи логов.
- `format`: Формат логов (`common` - текст, `json`).
- `level`: Уровень ведения журнала (`TRACE`, `DEBUG`, `INFO`, `WARN`, `ERROR`, `FATAL`, `PANIC`).
- `nocolor`: Отключение цветного формата в текстовом режиме.
- `maxSize`: Макс. размер файла в МБ до ротации.
- `maxBackups`: Макс. кол-во старых файлов, которые хранятся.
- `maxAge`: Макс. кол-во дней хранения старых файлов.
- `compress`: Сжимать ли старые файлы с помощью gzip.

```yml
accesslog:
  addInternals: false  # (default)
  filePath: "var/log/traefik/access.log"
  format: common  # (default)
  bufferingSize: 100
  filters:
    statusCodes:
      - "200"
      - "300-302"
    retryAttempts: true
    minDuration: "10ms"
```
- Конфигурация журналов доступа.
- `addInternals`: Журналирование внутренних ресурсов (например, `ping@internal` или `api@internal`).
- `filePath`: Путь к файлу для записи логов.
- `format`: Формат логов.
- `bufferingSize`: Асинхронная запись событий. Значение представляет кол-во строк, которые будут храниться в памяти перед записью.
- `filters`: Описывает какие события будет принимать этот лог.
- `statusCodes`: Разрешенный список статус кодов.
- `retryAttempts`: Сохранять, если есть повторные попытки доступа.
- `minDuration`: Сохранять, если запрос вышел за указанное время.