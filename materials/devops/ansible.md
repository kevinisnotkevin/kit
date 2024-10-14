# ANSIBLE

Ansible - это ПО с открытым исходным кодом, которое автоматизирует поставку программного обеспечения, управление конфигурацией и развёртывание приложений.

Особенности программы:

- В клиенте не установлено программное обеспечение или агент, который общается с сервером.
- Независимо от того, сколько раз вы вызываете операцию, результат будет одинаковым.
- Программа Ansible написанa на Python и использует YAML для написания команд.

С помощью Ansible можно решать следующие задачи:

- установка/удаление и конфигурирование ПО;
- создание/удаление пользователей;
- контроль пользовательских паролей/ключей;
- создание/удаление контейнеров/виртуальных машин;
- запуск различных скриптов/тестов.

## 1 Структура Ansible

В общем виде проект Ansible может включать в себя:

- переменные;
- playbook (сценарии);
- роли;
- inventory (списки групп хостов).

### 1.1 Переменные

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

### 1.2 Playbooks

Именно он используется для выполнения сценария. Это основной способ автоматизировать набор задач, которые нам нужно выполнить на удаленной машине.

Playbook представляет собой файл с расширением *.yml, в котором указаны необходимые действия для выполнения конкретного сценария.

Плейбук состоит из сценариев (play), которые выполняются в заданном порядке. Сценарий представляет собой список задач для определённой группы хостов.

Каждая задача связана с модулем, отвечающим за действие, и параметрами конфигурации. Playbook можно спокойно запускать несколько раз, т.к. большинство наших задач идемпотентны.

Плейбуки пишутся в YAML со стандартным расширением **.yml** с минимальным синтаксисом.

Структура playbook минимально должна выглядеть следующим образом

- hosts - указывается целевая группа хостов; также можно задать исключения по маске или указать несколько групп через двоеточие
- tasks или roles - выполняемое действие или роль.

Простой плейбук с двумя сценариями, в каждом по две задачи:

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

Также можно указать:

- become_user - пользователь, указывается пользователь, под которым будет выполняться действие; обычно это пользователь с привилегиями sudo
- remote_user - пользователь для ssh-соединения;
- include - дополнительно вызываемый сценарий, например валидация параметров приложения.

При запуске плейбука, Ansible выполняет задачи последовательно для всех указанных хостов. По умолчанию, задачи выполняются по очереди, одна за раз. Однако, поведение по умолчанию может быть скорректировано при необходимости с использованием стратегий.

Если при выполнении задачи возникает ошибка, Ansible прекращает выполнение плейбука для данного хоста, но продолжает работу с другими хостами, где задача была выполнена успешно. В процессе выполнения, Ansible отображает информацию о статусе подключения, названиях задач, статусе выполнения и наличии изменений.

По окончании работы Ansible предоставляет сводку выполнения плейбука, включающую информацию о выполненных и невыполненных задачах. Давайте рассмотрим пример для лучшего понимания. Для выполнения примера плейбука, используйте команду ansible-playbook.

### 1.3 Roles

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

### 1.4 Inventory

Inventory - это файл, который содержит информацию о устройствах, подключаемых к Ansible. В этом файле можно указывать устройства с использованием их IP-адресов или имен. Устройства могут быть перечислены как по одному, так и в виде групп. Формат файла - INI.

По умолчанию inventory хранится в `/etc/ansible/hosts`, но вы можете указать другое расположение с помощью флага `-i` или конфигурационного файла `ansible.cfg`.

```bash
[webservers]
host01.example.ru
host02.example.ru

[PC]
host03.example.ru
host04.example.ru
```

В этом примере мы используем формат INI, определяем четыре управляемых хоста и группируем их в две группы хостов

Ещё возможность определять псевдонимы для хостов в inventory. Например, мы можем использовать `host01`, если определим его в inventory следующим образом:

```bash
host01 ansible_host=host01.example.ru
```

### 1.5 Inventory и переменные

Ansible предлагает множество различных способов установки переменных, и одним из них является их определение в inventory. Давайте определим одну переменную для разной версии приложения для каждого хоста в нашем фиктивном inventory из предыдущего примера.

```bash
[webservers]
host01.example.ru app_version=1.1
host02.example.ru app_version=1.2
```

Хотя установка переменных в inventory возможна, этот способ обычно не является предпочтительным. Лучше храните отдельные файлы переменных хоста и группы для улучшения организации и ясности ваших проектов Ansible.

В том же каталоге, где мы храним наш файл inventory, мы можем создать две папки с именами `group_vars` и `host_vars`, содержащие наши файлы переменных. Например, у нас может быть файл `group_vars/webservers`, который содержит все переменные для веб-серверов:

```bash
http_post: 80
ansible_user: automation_user
```

## 1.3 Инфраструктура как код

Модель "инфраструктура как код (IaC)", иногда называемая "программируемой инфраструктурой", отражает подход, при котором настройка инфраструктуры осуществляется аналогично процессу программирования. Это подход, который по сути размывает границы между разработкой приложений и созданием окружения для этих приложений. Приложения могут содержать собственные скрипты, которые создают и управляют виртуальными машинами. Именно этот подход лежит в основе облачных вычислений и является неотъемлемой частью DevOps.

Популярность модели обусловлена двумя факторами:

- Вследствие растущей сложности облачной архитектуры работа вручную отнимает слишком много времени (не говоря о количестве ошибок);
- ручные изменения необходимо повторять по несколько раз, что ведет к неэффективному использованию финансов и времени команды инженеров.

"Инфраструктура как код" позволяет управлять виртуальными машинами на программном уровне, исключая необходимость ручной настройки и обновлений компонентов оборудования. Благодаря этому инфраструктура становится крайне "эластичной", то есть масштабируемой и воспроизводимой. Один и тот же набор кода может быть использован для развертывания и управления как одной, так и тысячами виртуальных машин. Преимуществами подхода "инфраструктура как код" являются скорость, экономичность и уменьшение рисков.

Одно из решений, которое необходимо принять перед применением IaC, — выбор подхода для автоматизации изменений окружения: императивный или декларативный. Чаще всего применяется декларативный подход. Главное отличие между ними заключается в том, что при императивном подходе программа указывает, как выполнить задачу, а при декларативном просто указывается конечная цель.

Для автоматического внесения изменений в инфраструктуру при императивном подходе можно использовать интерфейс командной строки (CLI). В соответствии с заданным скриптом изменения будут последовательно применяться к контейнерам, виртуальным машинам и виртуальным частным облакам. Этот подход позволяет подробно описать требуемые изменения, но если понадобится изменить конфигурацию после доставки на множество машин, придется вносить изменения в скрипт.

При декларативном подходе для автоматического внесения изменений указывается только конечная цель. Например, вместо перечисления всех шагов для создания конфигурации виртуальной машины через интерфейс командной строки, просто запрашивается создание виртуальной машины в определенном домене. Задача будет автоматически выполнена. Декларативный подход позволяет просто указать, что именно должно быть выполнено с помощью средств автоматизации.

## 1.4 Обнаружение как код

«Обнаружение как код» (DaC) - это концепция, которая расширяет принципы инфраструктуры как кода (IaC) и практики DevOps на область безопасности и обнаружения угроз. Он включает в себя автоматизацию настройки, развертывания и управления инструментами обнаружения и мониторинга безопасности, а также наборами правил с использованием кода и инструментов автоматизации.

Вот ключевые компоненты и идеи, связанные с «Обнаружение как код»:

1. **Автоматизация** . Точно так же, как «Инфраструктура как код» автоматизирует предоставление ресурсов инфраструктуры, «Обнаружение как код» автоматизирует развертывание и управление инструментами мониторинга и обнаружения безопасности. Это может включать настройку систем обнаружения вторжений (IDS), систем предотвращения вторжений (IPS), решений для управления журналами, платформ SIEM (управление информацией о безопасности и событиями) и т. д.
2. **Код** : конфигурации безопасности и наборы правил определяются в коде (обычно с использованием декларативных языков или сценариев). Этот код может контролироваться версиями, проверяться и тестироваться точно так же, как код приложения или код инфраструктуры. Это позволяет группам безопасности определять правила обнаружения, пороговые значения оповещений и ответы структурированным и воспроизводимым образом.
3. **Интеграция** : DaC интегрируется с существующими конвейерами и рабочими процессами DevOps. Это означает, что конфигурации безопасности и правила мониторинга могут быть легко интегрированы в процессы разработки и развертывания программного обеспечения. Безопасность становится неотъемлемой частью жизненного цикла разработки, а не отдельным этапом.
4. **Непрерывный мониторинг** : DaC продвигает идею непрерывного мониторинга и обнаружения угроз в реальном времени. Это гарантирует, что правила безопасности последовательно применяются и контролируются по мере развития инфраструктуры и приложений.
5. **Масштабируемость** : автоматизация позволяет легко масштабировать возможности мониторинга безопасности по мере роста инфраструктуры. По мере выделения новых ресурсов автоматически применяются необходимые конфигурации безопасности.
6. **Соответствие требованиям и аудит** . DaC может помочь в обеспечении соответствия, гарантируя, что политики и конфигурации безопасности последовательно применяются во всей среде. Он также предоставляет журналы аудита и документацию по конфигурациям и изменениям безопасности.
7. **Устойчивость** . Автоматизируя обнаружение нарушений безопасности и реагирование на них, организации могут быстрее и эффективнее реагировать на инциденты безопасности, снижая потенциальное воздействие нарушений.