# Grafana

По умолчанию используется встроенная БД SQLite для хранения конфигурации, пользователей, панелей и других данных.

```yml
services:
  grafana:
    image: grafana/grafana:10.4.12
    container_name: grafana
    ports:
      - "3000:3000"
    volumes:
      - "/srv/grafana:/var/lib/grafana"
    environment:
      GF_LOG_LEVEL: DEBUG
      GF_INSTALL_PLUGINS: "grafana-clock-panel, grafana-simple-json-datasource"
```
- Пример 