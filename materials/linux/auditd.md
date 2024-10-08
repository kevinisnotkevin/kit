# AUDITD


Одной из важных составляющих информационной безопасности инфраструктуры компании является SIEM - система управлением событиями и информацией безопасности.

Такую систему можно условно поделить на 2 основные части - подсистему сбора событий и подсистему анализа полученных событий. Правильная настройка первой поможет обнаружить вторжение на ранних этапах проникновения, облегчит написание событий тревоги (алертов), а если вас всё-таки взломали, то позволит разобраться, как и почему это произошло, какие действия выполняли злоумышленники.

Основным инструментом для сбора системных событий в линукс-системах является auditd. На основе этого инструмента созданы и другие, например, auditbeat, go-audit, которые дополняют основной функционал auditd. Поэтому, разобравшись с основными принципами работы базового инструмента, вам не составит труда воспользоваться и всеми остальными.

auditd (сокращение от Linux Audit Daemon) - нативный инструмент предназначенный для мониторинга событий операционной системы и записи их в журналы событий, разрабатываемый и поддерживаемый компанией RedHat. 

Был создан для тесного взаимодействия с ядром операционной системы - во время своей работы наблюдает за системными вызовами и может записывать события - чтение, запись, выполнение, изменение прав - связанные с файлами ОС. Таким образом, с его помощью можно отслеживать практически любые события, происходящие в операционной системе.

**Плюсы auditd:**
- работает на низком уровне мониторинга - отслеживает системные вызовы и действия с файлами;
- имеет неплохой набор утилит в комплекте для удобства работы;
- постоянно развивается и обновляется;
- бесплатен и легко устанавливается.
**Минусы auditd:**
- большинство событий, возникающих при атаках характерных для конкретного приложения, практически невозможно отслеживать поскольку на уровне системных вызовов и работе с файлами трудно отличить взлом от нормальной работы приложения. Такие события лучше отслеживать на уровне самих приложений;
- auditd может замедлять работу ОС. Это связанно с тем, что подсистеме аудита необходимо проводить анализ системных вызовов;
- не слишком гибок в настройке правил;
- на данный момент это не лучший инструмент для работы с контейнерами.

# Конфигурирование

Системные параметры хранятся в двух файлах. Первый называется auditd.conf и содержит настройки сервиса аудита, второй называется audit.rules. Audit.conf отвечает за сервис auditd по направлению какие события он записывает и в каком моменте времени. Второй файл содержит в себе правила и фильтры, используемые auditd при выполнении операций. В него администраторы вносят изменения, добавляют новые правила, а также правят текущие шаблоны или удаляют устаревшие.

```bash
-d – удалить правило из списка
-D – удалить все правила

-R — права на чтение
-W — разрешается изменять
-X — используется для исполняемых файлов, сервис запускает его от своего имени
-A — изменить атрибуты объекта

-F: задает путь к файлу и предоставляет права на поиск.
```
- Основные опции. Для создания, удаления и модификации правил аудита предназначена утилита auditctl
```bash
auditctl -a command,action -F path=name_file -F perm=permission
```
- Активация мониторинга за конкретным файлом. name_file - имя файла, за которым ведется наблюдение со стороны сервиса auditd, permission - права на доступ
```bash
auditctl -a список,действие -S имя_системного_вызова -F фильтры
```
- Второй пример синтаксиса написания правила

По сути, это всего лишь фильтры, которые позволяют сделать правила более точными. На деле из них используются только entry и exit, которые позволяют зарегистрировать либо сам факт обращения к системному вызову, либо его успешную отработку.

Второй параметр опции - `-a` - это действие, которое должно произойти в ответ на возникшее событие. Их всего два: never и always. В первом случае события не записываются в журнал событий, во втором – записываются.

Далее указывается опция `-S`, которая задает имя системного вызова, при обращении к которому должен срабатывать триггер (например, open, close, exit, и т.д.). Вместо имени может быть использовано числовое значение.

Необязательная опция `-F` используется для указания дополнительных параметров фильтрации события. 

```bash
auditctl -a exit,always -S open -F path=/etc/
```
- Например, если мы хотим вести журнал событий, связанных с использованием системного вызова open(), но при этом желаем регистрировать только обращения к файлам каталога /etc

Утилита auditctl используется для контроля поведения, получения состояния и добавления/удаления правил аудита, предоставляемого Linux ядром.

## Опции

```bash
# Установить максимальное количество доступных для аудита буферов, ожидающих обработки (значение в ядре по умолчанию - 64). Если все буфера заняты, то флаг сбоя будет выставлен ядром для его дальнейшей обработки
-b backlog

# Установить флаг блокировки. 0 позволит на время отключить аудит, включить его обратно можно, передав 1 как параметр. Если установлено значение опции 2, то защитить конфигурацию аудита от изменений. Каждый, кто захочет воспользоваться этой возможностью, может поставить эту команду последней в audit.rules. После этой команды все попытки изменить конфигурацию будут отвергнуты с уведомлением в журналах аудита. В этом случае, чтобы задействовать новую конфигурацию аудита, необходимо перезагрузить систему аудита
-e [0..2]

# Установить способ обработки для флага сбоя. 0=silent 1=printk 2=panic. Эта опция позволяет определить, каким образом ядро будет обрабатывать критические ошибки. Например, флаг сбоя выставляется при следующих условиях: ошибки передачи в пространство службы аудита, превышение лимита буферов, ожидающих обработки, выход за пределы памяти ядра, превышение лимита скорости выдачи сообщений. Значение по умолчанию: 1. Для систем с повышенными требованиями к безопасности, значение 2 может быть более предпочтительно
-f [0..2]

# Краткая помощь по аргументам командной строки
-h

# Игнорировать ошибки при чтении правил из файла
-i

# Вывести список всех правил по одному правилу в строке
-l

# Установить на правило ключ фильтрации. Ключ фильтрации - это произвольная текстовая строка длиной не больше 31 символа. Ключ помогает уникально идентифицировать записи, генерируемые в ходе аудита за точкой наблюдения
-k <key>

# Послать в систему аудита пользовательское сообщение. Это может быть сделано только из-под учетной записи root
-m <text>

# Установить ограничение скорости выдачи сообщений в секунду (0 - нет ограничения). Если эта частота не нулевая и она превышается в ходе аудита, флаг сбоя выставляется ядром для выполнения соответствующего действия. Значение по умолчанию: 0
-r <частота>

# Читать правила из файла. Правила должны быть расположены по одному в строке и в том порядке, в каком они должны исполняться. Следующие ограничения накладываются на файл: владельцем должен быть root и доступ на чтение должен быть только у него. Файл может содержать комментарии, начинающиеся с символа «#». Правила, расположенные в файле, идентичны тем, что набираются в командной строке, без указания «auditctl»
-R <file>

# Получить статус аудита
-s

# Добавить правило с указанным действием к концу списка. Заметьте, что запятая разделяет эти два значения. Отсутствие запятой вызовет ошибку. Ниже описаны имена доступных списков:
# - task - добавить правило к списку, отвечающему за процессы. Этот список правил используется только во время создания процесса - когда родительский процесс вызывает fork() или clone(). При использовании этого списка вы можете использовать только те поля, которые известны во время создания процесса: uid, gid и т.д
# - entry - добавить правило к списку, отвечающему за точки входа системных вызовов. Этот список применяется, когда необходимо создать событие для аудита, привязанное к точкам входа системных вызовов
# - exit - добавить правило к списку, отвечающему за точки выхода из системных вызовов. Этот список применяется, когда необходимо создать событие для аудита, привязанное к точкам выхода из системных вызовов
# - user - добавить правило, отвечающее за список фильтрации пользовательских сообщений. Этот список используется ядром, чтобы отфильтровать события приходящие из пользовательского пространства перед тем, как они будут переданы службе аудита. Необходимо отметить, что только следующие поля могут быть использованы: uid, auid, gid и pid. Все остальные поля будут обработаны, как если бы они не совпали
# - exclude - добавить правило к списку, отвечающему за фильтрацию событий определенного типа. Этот список используется, чтобы отфильтровывать ненужные события. Например, если вы не хотите видеть avc сообщения, вы должны использовать этот список. Тип сообщения задается в поле msgtype
-a <список,действие>
```

**ПРИМЕРЫ**

```bash
auditctl -a entry,always -S all -F pid=1005
```
- Увидеть все системные вызовы, используемые определенным процессом
```bash
auditctl -a exit,always -S open -F auid=510
```
- Увидеть все файлы, открытые определенным пользователем
```bash
auditctl -a exit,always -S open -F success!=0
```
- Увидеть неудачные попытки вызова системной функции open

## Конфигурационные файлы

Правила не обязательно задавать, используя командную строку. Во время старта демон auditd читает два файла: /etc/audit/auditd.conf и /etc/audit/audit.rules (в некоторых дистрибутивах они могут находиться прямо в /etc). Первый описывает конфигурацию демона и содержит такие опции: как имя журнала и его формат, частота обновления и другие параметры. Нет смысла их изменять, разработчики дистрибутива уже позаботились о грамотной настройке.

Второй файл содержит правила аудита в формате auditctl, поэтому все, что нужно сделать, чтобы получить правило, пригодное для записи в этот файл - просто опустить имя команды.

## Написание правила

**Пропишем правило, которое позволит наблюдать нам за изменением файла:**
```bash
auditctl -w /home/audit.txt -p wrxa -k audit_filter
```
- Прописываем правило, которое позволяет наблюдать за изменением файла. w - означает просмотр, /home/audit.txt - путь к файлу, p wrxa - разрешение на отслеживания, k audit_filter - фильтрующий ключ
```bash
nano /home/audit.txt
```
- Внесем изменения в файл
```bash
sudo ausearch -f /home/audit.txt
```
- Посмотрим доступ к файлу аудита, time - время изменения, auid=1000 - индентификатор пользователя, comm= - с помощью чего происходило редактирование

# Создание отчёта

```bash
aureport -f
```
- Создание отчета

# Постоянный аудит - редактирование конфигурационных файлов

```bash
nano /etc/audit/audit.rules
```
- Редактируем конфигурационный файл. В начале файла обычно размещаются несколько мета-правил, задающих конфигурацию и очищающих правила:
- Удаляем все правила из всех списков:`-D`
- Указываем количество буферов, хранящих сообщения аудита:`-b 8192`
- Что делать в чрезвычайной ситуации (например, если все буферы будут заполнены): `f 1`

```bash
0 – ничего не делать
1 – поместить сообщение в dmesg
2 – отправить ядро в панику (kernel panic)
```
