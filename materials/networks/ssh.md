# SSH

**SSH (Secure Shell)** - протокол, позволяющий производить удалённое управление операционной системой и туннелирование TCP-соединений (например, для передачи файлов). Схож по функциональности с протоколом Telnet но, в отличие от него, шифрует весь трафик, включая и передаваемые пароли. SSH допускает выбор различных алгоритмов шифрования. SSH-клиенты и SSH-серверы доступны для большинства сетевых операционных систем.

Раньше для удаленного доступа использовались протоколы Telnet, Rlogin, Rsh. Поддерживает несколько методов аутентификации (на основе пароля, открытого ключа, на основе хоста). Предоставляет два протокола передачи файлов: SCP, SFTP.

SSH позволяет безопасно передавать данные практически любого другого сетевого протокола. Таким образом, помимо удалённой работы на компьютере через командную оболочку, можно передавать звуковой или видео поток, а еще файлы.

По умолчанию протокол работает на 22 порту. Настройки по умолчанию можно изменить и выбрать любой доступный для назначения порт

## Функции SSH

Протокол SSH возник как попытка обезопасить открытые незащищенные соединения. Впоследствии его функции были значительно расширены.

Наиболее важными из них являются:

- Безопасные команды доступа к хосту. SSH дает возможность выполнять безопасные команды доступа к хосту, такие как ssh (удаленная оболочка), slogin (удаленный вход в систему), scp (удаленное копирование);
- X11 Forwarding. SSH предоставляет встроенный механизм для выполнения удаленных клиентов X Window.
- Port forwarding. SSH может выполнять переадресацию портов, передавая трафик c одного порта одной машины на другой порт другой машины. При этом передаваемый трафик шифруется;

## Принцип работы

Протокол работает в режиме клиент-сервер, где соединение устанавливает клиент, подключаясь к серверу. SSH-клиент управляет процессом установки соединения и использует криптографию с открытым ключом для проверки подлинности SSH-сервера. После успешного соединения SSH использует сильное симметричное шифрование и алгоритмы хеширования для обеспечения конфиденциальности и целостности данных, которыми обмениваются клиент и сервер.

Есть несколько шагов, которые нужно пройти, чтобы начать SSH-сеанс между компьютерами.

1. Сначала нужно обеспечить безопасный способ обмена сообщениями между компьютерами, то есть настроить зашифрованный канал.
2. Далее нужно проверить целостность данных, отправляемых клиентом.
3. После этого проверяется подлинность клиента.

После этих трёх шагов мы можем безопасно общаться с удалённым компьютером, делиться секретными данными, а также проверить, есть ли у клиента разрешение на доступ к хосту. Каждый из разделов ниже будет более подробно описывать эти действия.

**Настройка зашифрованного канала**

Вся информация, отправляемая с использованием SSH, зашифрована. Обе стороны должны знать и понимать способ шифрования.

Для шифрования передаваемых данных используется симметричное шифрование. Суть данного подхода заключается в том, что оба компьютера имеют одинаковый ключ шифрования, который называется «симметричный ключ». Симметричное шифрование работает очень хорошо, но только до тех пор, пока сторонние не имеют доступа к ключу.

Один компьютер может создать ключ и отправить в виде сообщения через интернет. Но сообщение ещё не будет зашифровано, поэтому любой, кто перехватит его, сразу же сможет расшифровать все следующие сообщения.

Решение этой проблемы состоит в использовании протокола обмена ключами Диффи-Хеллмана. Оба компьютера создают свой закрытый и открытый ключ. Вместе они образуют пару ключей. Компьютеры делятся своими открытыми ключами друг с другом через интернет. Используя свой закрытый и чужой открытый ключ стороны могут независимо сгенерировать одинаковый симметричный ключ.

**Верификация**

Следующий этап процесса установки сеанса SSH заключается в проверке того, что данные не были подделаны во время их передачи и что другой компьютер действительно является тем, за кого себя выдаёт.

Для верификации используют хеш-функцию. Это математическая функция, которая принимает входные данные и создаёт строку фиксированного размера.

Важной особенностью этой функции является то, что практически невозможно определить входные данные, зная лишь результат её работы.

После того как клиент и хост сгенерировали свои симметричные ключи, клиент использует хеш-функцию для генерации HMAC, что означает «код аутентификации сообщений, использующий хеширование». Клиент отправит этот HMAC на сервер для верификации.

Функция хеширования использует:

- симметричный ключ клиента,
- порядковый номер пакета,
- содержимое сообщения (зашифрованное).

Когда хост получает HMAC, он может использовать ту же самую хеш-функцию с этими тремя компонентами:

- собственный (идентичный клиентскому) симметричный ключ;
- порядковый номер пакета;
- зашифрованное сообщение.

Если сформированный хеш совпадает с HMAC, полученным от клиента, то мы можем быть уверены, что подключаемый компьютер — это компьютер с симметричным ключом, потому что только хост и клиент знают симметричный ключ, а другие компьютеры — нет.

Прелесть этого подхода в том, что мы не просто проверили личность клиента и убедились, что данные не были подделаны, но мы сделали это без передачи какой-либо секретной информации.

**Аутентификация**

Даже если мы используем симметричные ключи для безопасного общения, мы не знаем, имеет ли подключающийся компьютер разрешение на доступ к содержимому хоста. Для того, чтобы проверить это, необходимо произвести аутентификацию.

Многие используют аутентификацию по паролю. Клиент отправляет хосту зашифрованное сообщение, содержащее пароль. Хост его расшифровывает и ищет пароль в базе данных, чтобы удостовериться, есть ли у клиента разрешение на доступ. Использование пароля для аутентификации допустимо, но имеет свои недостатки, так как необходимо хранить все пароли на сервере.

Более безопасной является аутентификация по сертификату. Сформировав сертификат, клиент единожды вводит пароль для доступа к серверу и отправляет ему открытую часть сертификата. В дальнейшем ввод пароля не требуется. Этот подход считается более безопасным, чем просто использование пароля, поскольку не подразумевает хранение секрета пользователя на хосте.


Механика работы протокола SSH включает в себя не только шифрование данных для обеспечения конфиденциальности, но и аутентификацию для обеспечения безопасности соединения. Правильно настроенный SSH-сервер и использование безопасных методов аутентификации и шифрования делают SSH одним из наиболее надежных способов удаленного доступа к системам Linux.

- **Установление соединения:**
    - Процесс начинается с клиента, который отправляет запрос на соединение к серверу SSH.
    - Сервер слушает на определенном порту (обычно порт 22), и когда он получает запрос, устанавливается начальное сеансовое соединение.
- **Процедура аутентификации:**
    - После установления соединения сервер отправляет свой открытый ключ клиенту.
    - Клиент проверяет ключ сервера, чтобы убедиться, что это действительно сервер, к которому он хочет подключиться.
    - Клиент генерирует сессионный ключ, который будет использоваться для шифрования данных, и отправляет его серверу, зашифрованным с использованием открытого ключа сервера.
    - Сервер расшифровывает сессионный ключ с помощью своего закрытого ключа.
- **Защищенное соединение:**
    - Теперь, когда у клиента и сервера есть общий сессионный ключ, все данные, передаваемые между ними, шифруются с использованием этого ключа. Это обеспечивает конфиденциальность передаваемых данных.
    - Все команды и ответы от сервера также шифруются и передаются через защищенное соединение.
- **Аутентификация пользователя:**
    - После установления соединения, клиент и сервер проводят процедуру аутентификации пользователя.
    - Аутентификация может происходить с использованием пароля, ключей SSH (публичного и частного) или других методов, таких как двухфакторная аутентификация.
    - При успешной аутентификации пользователь получает доступ к удаленной системе и может выполнять команды на сервере.
- **Использование сессии:**
    - После успешной аутентификации пользователь может взаимодействовать с удаленной системой, выполнять команды, передавать файлы и манипулировать данными.
    - SSH также позволяет создавать защищенные туннели для безопасной передачи данных между клиентом и сервером.
- **Завершение сеанса:**
    - По завершении работы, клиент или сервер могут закрыть соединение, завершив сессию.

## Обеспечение безопасности

Безопасность протокола достигается использованием нескольких решений, которые сводят к минимуму риск использования соединения:

- Шифрование соединение, которое может выполняться одним из методов, выбранных в процессе переговоров. Шифрованное соединение не позволяет просто перехватить и использовать трафик. Выбор алгоритма шифрования делает систему более гибкой, позволяя не использовать алгоритмы, в которых обнаружены слабые места или которые не может поддерживать одна из сторон;
- Аутентификация сервера выполняется при любом соединении. Это не позволяет выполнить подмену сервера или подмену трафика;
- Аутентификация клиента может выполняться одним из нескольких доступных способов. Это с одной стороны может повысить надежность аутентификации, с другой -- делает систему более гибкой и упрощает ее использование;
- Проверка целостности пакетов позволяет отследить любые незаконные изменения в трафике соединения. При обнаружении таких изменений, соединение немедленно разрывается;
- Временные параметры аутентификации не позволяют воспользоваться данными соединения в том, случае, если спустя некоторое время после перехвата оно все-таки было расшифровано. Устаревание обычно происходит через час;

SSH защищает от:

- Подмены IP-адресов (IP-spoofing), когда удаленный хост посылает пакеты от имени другого хоста;
- Подмены DNS-записей (DNS-spoofing), когда изменяется запись на сервере DNS и в результате соединение устанавливается не с желаемым хостом, а с тем, на который указывает новая запись;
- Перехвата открытых паролей и прочих данных, которые передаются в открытом виде и любой, кто имеет физический доступ к каналу, может их узнать.

Рассмотрим немного подробнее аутентификацию клиента

**Методы аутентификации клиентов,** которые использует SSH:

- Host-based аутентификация
- Аутентификация с помощью открытых ключей
- Kerberos-аутентификация
- Парольная аутентификация

**По хостам**. Метод аналогичный используемому в r-командах. В том случае, если соединение устанавливается с привилегированного порта, и файл .rhosts позволяет вход в систему, он разрешается. Этот метод является потенциально небезопасным, рекомендуется не использовать его. Для повышения уровня своей безопасности метод может быть дополнен RSA-аутентификацией клиентского хоста.

**Открытый ключ.** Клиент отправляет серверу открытый ключ. Если сервер знает его, он просит клиента доказать, что тот знает и секретный ключ тоже. Если клиент может это доказать, значит аутентификация считается успешной.

**Керберос**. Аутентификация проводится по схеме v5 Kerberos.

**Пароль.** В самом крайнем случае, если не удалось провести аутентификацию не одним из перечисленных способов, используется традиционная аутентификация при помощи пароля. Принцип аутентификации аналогичен тому, какой, например, используется в Telnet с той разницей, что пароль передается по зашифрованному каналу.

## Применение

- **Удаленное администрирование:** Администраторы серверов могут использовать SSH для удаленного управления и настройки серверов без физического доступа к ним.
- **Передача файлов:** SSH может использоваться для безопасной передачи файлов между компьютерами с помощью протокола SFTP (SSH File Transfer Protocol) или SCP (Secure Copy).
- **Туннелирование:** SSH позволяет создавать защищенные сетевые туннели для безопасной передачи данных через ненадежные сети или доступа к ресурсам, ограниченным брандмауэром.
- **Удаленный доступ к текстовым и графическим приложениям:** SSH может использоваться для доступа к текстовым консолям, а также для удаленного запуска графических приложений.
- **Автоматизация задач:** Администраторы могут использовать SSH для автоматизации рутинных задач и скриптов удаленного управления серверами.

## Способы повышения защиты

SSH является мощным инструментом для безопасной удаленной работы и управления системами, но его безопасность зависит от правильной настройки и соблюдения рекомендаций по защите.

- **Использование ключей для аутентификации:** Вместо паролей можно использовать ключи SSH, которые обычно являются более безопасными
- **Ограничение доступа:** Настройка файрволла и прав доступа к SSH-серверу для ограничения доступа только для определенных IP-адресов
- **Использование двухфакторной аутентификации:** Дополнительная защита, которая требует два разных метода аутентификации для доступа
- **Регулярное обновление SSH-сервера:** Важно обновлять SSH-сервер и другое ПО системы, чтобы устранить известные уязвимости
- **Мониторинг и журналирование:** Ведение журналов аутентификации и мониторинг активности SSH для выявления подозрительных действий
- **Использование специализированных инструментов безопасности:** Использование инструментов, таких как fail2ban или DenyHosts, для обнаружения и блокировки атак
- Чтобы снизить риск несанкционированного доступа, рекомендуется отключить прямой вход в систему root, использовать стандартную учетную запись пользователя с привилегиями sudo, ограничить доступ по SSH определенным IP-адресам
- Для повышения безопасности необходимо сменить стандартный номер порта, запретить входить напрямую пользователем root в конфигурациях.

## Туннелирование (порт-форвардинг) в SSH

Туннелирование (порт-форвардинг) в SSH - это механизм, который позволяет направлять сетевой трафик между клиентом и сервером через защищенное соединение SSH. Этот процесс позволяет пользователям создавать безопасные сетевые туннели для передачи данных через ненадежные или непрозрачные сети, а также для обеспечения доступа к удаленным ресурсам через брандмауэры и фильтры.

Принцип работы туннелирования в SSH:
- **Установление SSH-соединения:**
    - Процесс начинается с установления SSH-соединения между клиентом и сервером. Это соединение обеспечивает шифрование и аутентификацию для безопасной связи.
- **Определение порта для туннеля:**
    - Пользователь определяет порт на локальной машине (клиенте), который будет использоваться для создания туннеля.
    - Этот порт может быть любым доступным портом на клиентской машине, например, портом 8080.
- **Запуск команды SSH с параметрами туннелирования:**
    - Пользователь запускает команду SSH с параметрами, указывающими на создание туннеля. Например: `ssh -L 8080:remote-server:80 user@remote-server`. В этой команде **`-L`** указывает на локальный порт (8080), который будет проксировать трафик на удаленный сервер (**`remote-server`**) на порт 80.
        - **`ssh:`** Это команда, которая запускает клиент SSH для установления соединения с удаленным сервером.
        - **`L:`** Это опция SSH, которая указывает на создание локального туннеля. Опция **`L`** используется для указания порта и адреса удаленного сервера, к которому будет направлен туннель.
        - **`8080:`** Это локальный порт на клиентской машине, который будет использоваться для создания туннеля. В данном случае, это локальный порт 8080.
        - **`remote-server:`** Это адрес удаленного сервера, к которому будет направлен туннель. Замените **`remote-server`** на реальный IP-адрес или доменное имя удаленного сервера.
        - **`80:`** Это порт на удаленном сервере, к которому будет направлен туннель. В данном случае, это порт 80, который обычно используется для HTTP-трафика.
        - **`user:`** Это имя пользователя, с которым вы планируете войти на удаленный сервер.
        - **`@:`** Это символ, разделяющий имя пользователя и адрес удаленного сервера.
        - **`remote-server:`** Снова адрес удаленного сервера, на который вы хотите подключиться.
- **Установка туннеля:**
    - Когда SSH-соединение установлено и пользователь вошел в систему на удаленном сервере, туннель также устанавливается.
    - Локальный порт (8080) на клиентской машине теперь слушает запросы.
- **Передача трафика через туннель:**
    - Когда локальный клиент отправляет запрос на локальный порт (8080), SSH перенаправляет этот трафик через защищенное соединение на удаленный сервер.
    - Удаленный сервер, в свою очередь, направляет трафик на указанный порт (80) на удаленном сервере.
    - Это позволяет локальному клиенту получать данные с удаленного сервера через безопасное SSH-соединение, обеспечивая конфиденциальность и целостность данных.
- **Использование туннеля:**
    - Теперь пользователь может использовать локальный порт (8080) на клиентской машине для доступа к удаленным ресурсам, как если бы они были локальными.
    - Таким образом, можно обходить ограничения сети, брандмауэры и фильтры, а также обеспечить безопасную передачу данных.
