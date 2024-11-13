# ОРКЕСТРАЦИЯ

**Оркестрация контейнеров** - это процесс автоматизации объединения контейнеров в сеть и управления ими.

- Оркестрация позволяет автоматизировать количество рутинных задач, связанных с управлением контейнерами. Это снижает риск человеческих ошибок и ускоряет процесс разработки и развёртывания приложений. Например, благодаря использованию оркестраторов вы можете развернуть или обновить сразу все компоненты микросервисного приложения заданных версий с целью тестирования новой функциональности.
- Благодаря оркестрации можно легко масштабировать сервисы и компоненты, добавляя или удаляя контейнеры в зависимости от нагрузки, что обеспечивает высокую доступность и производительность.
- В случае выхода одного или нескольких узлов кластера из строя оркестратор запустит контейнеры на оставшихся узлах с целью сохранения работоспособности микросервисного приложения.
- Большинство оркестраторов предоставляет механизмы для применения политик безопасности и контроля доступа к управлению контейнерами, что повышает уровень защиты приложений.
- Оркестраторы, как правило, имеют механизмы, облегчающие сбор метрик и логов контейнеров. Это важно для диагностики проблем и оптимизации производительности.

![](/materials/images/devops/orchestrators.png)
- Сравнение оркестраторов.

Основное отличие Docker Compose от Docker Swarm, Kubernetes и Nomad - он предназначен для локальной разработки и тестирования приложений. Позволяет запускать связанные контейнеры на локальной машине и не поддерживает кластерные инсталляции, в которых контейнеры запускаются на множестве узлов кластера.

Docker Swarm, Kubernetes и Nomad предназначены для управления контейнерами в больших масштабах и обеспечения высокой доступности и надёжности приложений в производственной среде. Они предлагают более сложные механизмы управления ресурсами, масштабирования и обеспечения отказоустойчивости, которые необходимы для развёртывания сложных приложений в облаке или на серверах компании.

## DOCKER COMPOSE

- [Подробнее про Docker Compose](/materials/devops/docker_compose.md)

## DOCKER SWARM

- [Подробнее про Docker Swarm](/materials/devops/docker_swarm.md)

## NOMAD

**Nomad** - это оркестратор, который предназначен для управления, распределением контейнеров по узлам кластера и обеспечения их высокой доступности.

**ПРЕИМУЩЕСТВА**

- Nomad предлагает бо́льшую гибкость в настройке и управлении контейнерами в сравнении с предыдущими оркестраторами. Он позволяет задавать различные стратегии распределения контейнеров по узлам, включая распределение по ресурсам, географическому расположению и другим параметрам.
- Nomad имеет относительно небольшой размер и потребляет меньше ресурсов, чем Kubernetes. Это делает его подходящим для небольших и средних проектов, где не требуется сложная инфраструктура.
- Nomad может работать на различных платформах, включая Linux, macOS и Windows. Это делает его универсальным решением для разных сред разработки.
- Nomad имеет простой и понятный интерфейс командной строки, а также встроенный веб-интерфейс, что упрощает его использование и настройку.
- Nomad обеспечивает высокую доступность приложений за счёт автоматического перезапуска контейнеров в случае их сбоя.
- Nomad поддерживает горизонтальное масштабирование приложений путем добавления новых узлов в кластер. Такой возможности нет у Docker Compose.

## KUBERNETES

- [Подробнее про Kubernetes](/materials/devops/k8s.md)