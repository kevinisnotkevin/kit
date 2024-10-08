# SYSTEMCTL

- `start <uмя.service>` -> Запускает сервис
- `stop <uмя.service>` -> Останавливает сервис
- `restart <uмя.service>` -> Перезапускает сервис
- `try-restart <uмя.service>` -> Перезапуск сервиса только, если он запущен
- `reload <uмя.service>` -> Перезагружает конфигурацию сервиса
- `status <uмя.service>` -> Отображает подробное состояние сервиса
- `is-active <uмя.service>` -> Отображает только строку active (сервис запущен) или inactive (остановлен)
- `list-units --type service --all` -> Выводит состояние всех сервисов
- `еnаblе <uмя.service>` -> Включает сервис (обеспечивает его автоматический запуск)
- `disaЬle <uмя.service>` -> Отключает сервис (сервис не будет автоматически запускаться при запуске системы)
- `rееnablе <uмя.service>` -> Деактивирует сервис и сразу его использует
- `list-unit-files --type service` -> Выводит список всех сервисов и сообщает, какие из них активированы, а какие - нет