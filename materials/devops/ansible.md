# ANSIBLE

**Ansible** - это инструмент автоматизации без агентов, который устанавливается на одном хосте (control node) для управления другими устройствами (managed nodes) удаленно с помощью SSH.

**Control node** - это система, на которой установлен ansible.

**Managed node** - это удаленная система или хост, которым управляет ansible.

**ОСОБЕННОСТИ**

- В клиенте не установлено ПО или агент, который общается с сервером.
- Ansible написанa на Python и использует YAML для написания команд.
- В качестве управляющего узла (машины, на которой работает Ansible) можно использовать практически любую UNIX-подобную машину с установленным Python. Сюда входят Red Hat, Debian, Ubuntu, macOS, BSD и Windows в рамках дистрибутива подсистемы Windows для Linux (WSL). Windows без WSL изначально не поддерживается в качестве узла управления.
- Управляемый узел (машина, которой управляет Ansible) не требует установки Ansible, но требует Python для запуска кода Python, сгенерированного Ansible. Управляемому узлу также необходима учетная запись пользователя, которая может подключаться через SSH к узлу с помощью интерактивной оболочки POSIX.
- Проект Ansible может включать в себя: переменные, playbooks, роли, inventories.

**ПРИМЕНЕНИЕ**

- Установка, удаление, конфигурирование ПО
- Создание, удаление пользователей
- Контроль пользовательских паролей и ключей
- Создание, удаление контейнеров и виртуальных машин
- Запуск различных скриптов/тестов

## Установка

```bash
# Полный пакет
sudo apt install ansible
# Или
# Минимальный пакет
sudo apt install ansible-core
```
- Установка на Linux.

## Переменные

**Переменные** используются для хранения значений, которые могут применяться в playbook.

Переменные можно определять:

- в специальных файлах для группы/устройства;
- в inventory;
- в playbook;
- в ролях, которые затем используются;
- переменные, передаваемые при вызове playbook.

Специальные файлы с переменными могут хранится в 2 директориях:

- `group_vars/` - групповые переменные(общие). При необходимости можно создать файл all, содержащий переменные, относящиеся ко всем группам.
- `host_vars/` - переменные отдельных хостов (частные). В них имена файлов должны совпадать с именем или адресом хоста.

Названия файлов в директориях не принципиальны, главное, чтобы имя директории совпадало с именами в inventory.

Файлы переменных должны быть в формате YAML.

Для каждой группы или хоста можно не создавать отдельную директорию, можно просто создать файл и записать в нем все необходимые переменные.

Приоритеты переменных (в порядке возрастания):

- установленные в ролях;
- заданные в inventory;
- групповые переменные (общие);
- групповые переменные (частные);
- переданные из playbook;
- передаваемые из команды (-e / —extra-vars).

## Playbook

**Playbook** - это сценарий для настройки управляемых узлов в формате `YML`. Состоит из упорядоченного списка задач (**plays**).

**Play** - это обьект playbook, который сопоставляет управляемые узлы из инвентаря с задачами (**tasks**). Он содержит переменные, роли и упорядоченные списки задач и может запускаться повторно, так как большинство задач идемпотентны.

**Tasks** - это действие, которое будет применено к управляемому узлу. Можно выполнить одну задачу один раз с помощью специальной команды. По умолчанию, задачи выполняются последовательно. Если при выполнении задачи возникает ошибка, то выполнение плейбука прекращается для данного хоста.

**Module** - это код, который копируется и выполняется на каждом управляемом узле для выполнения действия, определенного в каждой задаче. Каждый модуль имеет особое применение: от администрирования пользователей в базе данных определенного типа до управления интерфейсами VLAN на сетевом устройстве определенного типа.

В процессе выполнения отображается информация о статусе подключения, названиях задач, статусе выполнения и наличии изменений. По окончании работы выводится информация о выполненных и невыполненных задачах.

**ПАРАМЕТРЫ**

- become_user - пользователь, под которым будет выполняться действие; обычно это пользователь с привилегиями sudo
- remote_user - пользователь для ssh-соединения;
- include - дополнительно вызываемый сценарий, например валидация параметров приложения.
- hosts - указывается целевая группа хостов. Также можно задать исключения по маске или указать несколько групп через двоеточие.
- tasks или roles - выполняемое действие или роль.

```yml
- name: First play
  hosts: myhosts
  tasks:
    - name: Ping my hosts
      ansible.builtin.ping:

    - name: Print message
      ansible.builtin.debug:
        msg: Hello world
```
- Сценарий, который пингует узлы и выводит debug сообщение.

```bash
- name: Example Simple Playbook
  hosts: all
  become: yes

  tasks:
  - name: Copy file example_file to /tmp with permissions
    ansible.builtin.copy:
      src: ./example_file
      dest: /tmp/example_file
      mode: '0644'

  - name: Add the user 'bob' with a specific uid 
    ansible.builtin.user:
      name: bob
      state: present
      uid: 1040

- name: Update postgres servers
  hosts: databases
  become: yes

  tasks:
  - name: Ensure postgres DB is at the latest version
    ansible.builtin.yum:
      name: postgresql
      state: latest

  - name: Ensure that postgresql is started
    ansible.builtin.service:
      name: postgresql
      state: started
```
- Плейбук с двумя сценариями, в каждом по две задачи.

## Role

**Роль** является структурированным плейбуком, содержащий набор задач, обработчиков событий, переменных, файлов, шаблонов и описанных зависимостей.

В архитектуре проекта обычно сервера разделяются по своему предназначению и выполняют определенную роль - web-сервер, сервер баз данных, почтовый сервер и т. д. Каждому из серверов требуется отличающийся набор пакетов и настроек для правильного выполнения своей роли. С ростом количества серверов будет расти количество наборов инструкций (playbook), которые будут использоваться повторно.

`Ansible` позволяет удобно организовать и структурировать наборы инструкций в соответствии с ролями серверов.

Пример структуры набора инструкций с ролями выглядит так:

```yaml
- hosts: servers 
  roles: 
	   - web
	   - db
	   - post
```

Файловая структура ролей может выглядеть таким образом:

```bash
site.yml
servers.yml
roles/
   web/
     files/
     templates/
     tasks/
     handlers/
     vars/
     defaults/
     meta/
   db/
     files/
     templates/
     tasks/
     handlers/
     vars/
     defaults/
     meta/
   post/
     files/
     templates/
     tasks/
     handlers/
     vars/
     defaults/
     meta/
```

Файловая структура не обязательно должна выглядеть так - если какой-либо каталог отсутствует в роли, то он будет проигнорирован и набор инструкций будет выполняться дальше.

О ролях можно писать очень много и долго, поэтому я лучше порекомендую Ansible Galaxy - крупнейший репозиторий ролей `Ansible`, там можно брать уже готовые роли (например, для обучения) или делиться своими ролями.

## Inventory

**Inventory** - это файл, который содержит информацию об управляемых узлах.

В этом файле можно указывать устройства с использованием их IP-адресов или имен. Устройства могут быть перечислены как по одному, так и в виде групп. Формат файла может быть INI или YAML.

По умолчанию inventory хранится в `/etc/ansible/hosts`, но можно указать другое расположение с помощью флага `-i` или конфигурационного файла `ansible.cfg`.

**ПРО ГРУППЫ**

- Имена групп чувствительны к регистру.
- Нельзя использовать пробелы, дефисы и цифры в начале имени группы.
- Группируйте хосты по топологии, например: db, web, leaf, spine.
- Группируйте хосты по географическому расположению, например: datacenter, region, floor, building.
- Группируйте хосты по этапам, например: development, test, staging, production.


```ini
[myhosts]
0.0.0.0
example.ru
```
- В этом примере определяем два управляемых хоста и группируем в одну группу хостов в INI.
```yaml
myhosts:
  hosts:
    my_host_01:
      ansible_host: 0.0.0.0
    my_host_02:
      ansible_host: example.com
```
- Аналогичный пример, но в YAML.

```bash
ansible-inventory -i inventory.ini --list
```
- Далее, проверяем инвентарь.

```bash
ansible myhosts -m ping -i inventory.ini
```
- Пингуем хосты из группы `myhosts` из инвентаря.

```bash
host01 ansible_host=host01.example.ru
```
- Можно определять псевдонимы для хостов в inventory. Например, можно использовать `host01`.

```yml
webservers:
  hosts:
    webserver01:
      ansible_host: 192.0.1.100
      http_port: 80
    webserver02:
      ansible_host: 192.0.1.101
      http_port: 81
  vars:
    ansible_user: my_server_user
```
- Инвентарь с переменной `ansible_user`, определенной для группы узлов.

```yml
webservers:
  hosts:
    webserver01:
      ansible_host: 192.0.1.100
      http_port: 80
    webserver02:
      ansible_host: 192.0.1.101
      http_port: 81
```
- Инвентарь с переменными `http_port`, определенными для каждого узла.

Хотя установка переменных в inventory возможна, этот способ обычно не является предпочтительным. Лучше храните отдельные файлы переменных хоста и группы для улучшения организации и ясности ваших проектов Ansible.

```bash
http_post: 80
ansible_user: automation_user
```
- В том же каталоге, где мы храним наш файл inventory, мы можем создать две папки с именами `group_vars` и `host_vars`, содержащие наши файлы переменных. Например, у нас может быть файл `group_vars/webservers`, который содержит все переменные для веб-серверов.

```yml
leafs:
  hosts:
    leaf01:
      ansible_host: 192.0.2.100

spines:
  hosts:
    spine01:
      ansible_host: 192.0.2.120

network:
  children:
    leafs:
    spines:

webservers:
  hosts:
    webserver01:
      ansible_host: 192.0.2.140

datacenter:
  children:
    network:
    webservers:
```
- Пример с метагруппой, которая организует несколько групп в инвентаре. Этот пример инвентаризации содержит `network` метагруппу, включающую все сетевые устройства, и метагруппу `datacenter`, включающую сетевую группу и все веб-серверы.