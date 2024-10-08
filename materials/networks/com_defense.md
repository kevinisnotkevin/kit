# Защита коммутаторов

## Port Security

Port security - технология на основе мак адресов устройств регулирует, какие подключенные компьютеры могут передавать данные через конкретный сетевой интерфейс коммутатора.

Используется для:
- Предотвращения несанкционированной смены MAC-адреса сетевого устройства или подключения к сети
- Предотвращения атак, направленных на переполнение таблицы коммутации

После настройки порт не передает пакеты, если MAC-адрес отправителя не указан как разрешенный. Кроме того, можно указывать не конкретные MAC-адреса, разрешенные на порту коммутатора, а ограничить количество MAC-адресов, которым разрешено передавать трафик через порт.

Технология работает на основе таблицы мак адресов коммутатора. Напомню, что в данную таблицу записываются мак адреса компьютеров, обменивающихся информацией в сети. Коммутатор сопоставляет эти мак адреса со своими физическими интерфейсами, благодаря чему происходит процесс коммутации.

Получается, что мы можем назначить список разрешенных мак адресов для конкретного интерфейса. В таком случае указанные мак адреса коммутатор допустит до процесса коммутации, а любые другие – нет.

### Безопасные MAC-адреса

Коммутатор поддерживает такие типы безопасных MAC-адресов:

- Статические MAC-адреса:
    - задаются статически командой switchport port-security mac-address mac-address в режиме настройки интерфейса
    - хранятся в таблице адресов
    - добавляются в текущую конфигурацию коммутатора
- Динамические MAC-адреса:
    - динамически выучиваются
    - хранятся только в таблице адресов
    - удаляются при перезагрузке коммутатора
- Sticky MAC-адреса:
    - могут быть статически настроены или динамически выучены
    - хранятся в таблице адресов
    - добавляются в текущую конфигурацию коммутатора. Если эти адреса сохранены в конфигурационном файле, после перезагрузки коммутатора, их не надо заново перенастраивать

### Режимы реагирования

Нарушением безопасности для port security считаются ситуации:
- максимальное количество безопасных MAC-адресов было добавлено в таблицу адресов и хост, чей MAC-адрес не записан в таблице адресов пытается получить доступ через интерфейс
- адрес, выученный или настроенный как безопасный на одном интерфейсе, появился на другом безопасном интерфейсе в том же VLAN

На интерфейсе могут быть настроены такие режимы реагирования на нарушения безопасности:

- Protect

В случае нарушения, кадры Ethernet с неавторизованным MAC-адресом источника отбрасываются. В этом режиме нарушения коммутатор не предоставляет уведомления о событии.

- Restrict

В случае нарушения кадры Ethernet с неразрешенными MAC-адресами источников отбрасываются. Коммутатор обеспечивает уведомление о нарушениях безопасности и ведет подсчет количества нарушений.

- Shutdown

Когда происходит нарушение, порт отключается и автоматически регистрируется в журнале. Для возобновления работы порт необходимо перезагрузить вручную.

### Настройка Port Security

Так как после команды switchport port-security, сразу включается Port security с настройками по умолчанию, то ее стоит давать вначале только если настройки по умолчанию подходят.

Включается Port Security командной:
```
switch(config-if)# switchport port-security
```

На коммутаторах Cisco такие настройки по умолчанию для функции port security:
- Port security - выключен
- Запоминание sticky-адресов - выключено
- Максимальное количество безопасных MAC-адресов на порту - 1
- Режим реагирования на нарушения - shutdown
- Время хранения адресов: отключено. Значение aging time - 0, для статических адресов - отключено, тип времени - абсолютное

При самостоятельной настройке видим поля:
- aging - задается временной интервал, после которого динамический МАС-адрес может быть переписан
- mac-address - указываем безопасный MAC-адрес
- maximum - указываем максимальное количество MAC-адресов, которые смогут подключиться
- violation - режим реагирования на нарушение

Рассмотрим их подробнее

Aging. Имеет три параметра:
- Static – включить aging для настроенных безопасных адресов
- Time – время устаревания (в минутах от 1 до 1440)
- Type – тип устаревания (absolute – абсолютное (по умолчанию); inactivity – на основе периода бездействия)

Mac-address. Можно указать:
- H.H.H – разрешённый адрес
- Forbidden – запрещённый адрес
- Sticky – коммутатор будет учить новые адреса

Maximum – указываем сколько адресов можно запомнить (1 - 4097)

Violation – упоминалось раньше

Устанавливаем что нужно, что не нужно пропускаем. Если до этого не включили настройки по умолчанию активируем технологию командой switchport port-security без опций.

Тогда получаются следующие настройки:
```
Switch(config-if)#switchport port-security 
Switch(config-if)#switchport port-security maximum 4 
Switch(config-if)#switchport port-security mac-address 0005.5E80.22A3 
Switch(config-if)#switchport port-security mac-address sticky 
Switch(config-if)#switchport port-security violation shutdown
```

## Защита от Rogue DHCP Server

Цель данной атаки - подмена DHCP-сервера. При одновременном нахождении в сети двух DHCP-серверов, один из которых «вражеский», некоторая часть клиентов сконфигурирует у себя неправильные адреса и прочие сетевые реквизиты.

Вследствие подмены шлюза по умолчанию неавторизованный DHCP-сервер получит возможность прослушивать весь трафик клиентов, перенаправляя в дальнейшем пакеты по назначению. Таким образом мы имеем простейшую реализацию атаки типа MitM (Man in the Middle), которая может быть осуществлена в большинстве современных сетей.

Стоит отметить, что чаще всего атака с подменой DHCP-сервера не является атакой, как таковой. Распространены случаи, когда по незнанию в сеть подключается SOHO роутер с настроенным DHCP-сервером, причем подключается он LAN-портом. После этого у клиентов, успевших получить у него IP-адреса, наблюдаются как минимум существенные потери скорости, а чаще всего полная невозможность использования локальных и глобальных ресурсов.

Простейший способ защиты от атак подобного рода - включение на всех коммутаторах функции DHCP snooping. Далее необходимо определить два типа портов:
- Доверенные - порты коммутатора, к которым подключается DHCP-сервер, либо другой коммутатор
- Недоверенные - порты для клиентских подключений, за которыми DHCP-сервер находиться не может, зато вполне может находиться атакующее устройство

В данном случае DHCP snooping необходим для того, чтобы указать коммутатору, что следует обращать внимание на пакеты DHCP offer и acknowledgment, проходящие сквозь него, и не допускать прохождения данных пакетов с недоверенных портов. Также широковещательные запросы от клиента (discover и request) теперь будут перенаправляться только на доверенные порты.

Для конфигурирования функции DHCP snooping необходимо:
1. Включить ее на коммутаторе: _SW(config)#ip dhcp snooping_
2. Указать для каких VLAN требуется отслеживать пакеты: _SW(config)#ip dhcp snooping vlan_
3. Указать доверенные порты на коммутаторе (все остальные по умолчанию становятся недоверенными): _SW(config-if)#ip dhcp snooping trust_

## Защита от DHCP starvation

Еще одна атака, осуществляемая при помощи протокола DHCP. DHCP-пул, из которого клиенты получают IP-адреса, ограничен. Например, это может быть 253 адреса (при маске 255.255.255.0). Для нормальной работы сети этого должно хватить всем клиентам, но атака DHCP starvation стремится изменить данную ситуацию. Как происходит действие:
1. Атакующее устройство запрашивает себе IP-адрес у DHCP-сервера и получает его
2. MAC-адрес атакующего устройства изменяется и оно запрашивает следующий, уже другой IP-адрес, маскируясь под нового клиента
3. Такие действия повторяются до тех пор, пока весь пул IP-адресов на сервере не будет исчерпан

Далее возможны два последствия, в зависимости от того, что является целью атаки:
- Отказ в обслуживании. IP-адреса исчерпаны, и новые хосты не могут получить их. Таким образом, их взаимодействие с сетью на этом закончится
- Подмена DHCP-сервера. DHCP starvation отлично комбинируется с предыдущей атакой. Так как на основном DHCP-сервере свободных адресов не осталось, он выбывает из игры, и 100% клиентов достается вражескому атакующему DHCP-серверу

Самый простой способ защиты - ограничение числа MAC-адресов на порту коммутатора. Реализуется это с помощью функции port-security:
1. _Переводим порт в access режим: SW(config-if)#switchport mode access_
2. _Включаем port-security на интерфейсе: SW(config-if)#switchport port-security_
3. _Ограничиваем число MAC-адресов на интерфейсе: SW(config-if)switchport port-security maximum_
4. Выбираем способ изучения MAC-адресов коммутатором: _SW(config-if)#switchport port-security mac-address <mac-address | sticky>_
5. Задаем тип реагирования на превышение числа разрешенных MAC-адресов: _SW(config-if)#switchport port-security violation <protect | restrict | shutdown>_

Таким образом атакующее устройство просто не сможет исчерпать лимит IP-адресов DHCP-пула, так как коммутатор не позволит ему безнаказанно изменять свой MAC-адрес.

Также здесь может помочь все тот же DHCP snooping, а именно команда _SW(config-if)ip dhcp snooping limit rate_

Данная команда ограничивает количество DHCP пакетов в секунду на порт (рекомендуется не более 100 pps), а при превышении данного ограничения переводит порт в состояние err-disable, то есть временно выключает его. Обычный клиент не превысит данный лимит, а вот атакующее устройство, генерирующее сотни MAC-адресов в секунду, будет обнаружено.

## Защита от CAM-table overflow

Напомню, что данная атака заключается в следующем: генерируется большое количество адресов и коммутатор записывает их в свою таблицу, а реальные адреса реальных устройств постепенно выходят из нее. В таком случае коммутатор начнет, рассылать кадры, адресованные конкретному получателю на все порты, находящиеся в том же VLAN, следовательно у атакующего устройства появится возможность перехватить и прочитать их.

Следует заметить, что все коммутаторы, подключенные к атакованному также подхватят фальшивые MAC-адреса и начнут вести широковещательную рассылку всех кадров.

Методы защиты
1. Port-security на всех access-портах с лимитированием максимального количество MAC-адресов.
2. Шифрование трафика – в таком случае хоть все кадры и будут рассылаться широковещательно, а производительность сети сильно ухудшится, информация, попавшая в чужие руки, не будет прочитана.

## Защита от VLAN hopping

Напомню о двух атак типа Vlan Hopping, которые мы ранее рассматривали

**Подмена коммутатора**

Если злоумышленник может генерировать сообщения динамического транкового протокола (DTP) со своего компьютера, между его компьютером и коммутатором может быть сформирована магистральная линия. Как только магистральный канал сформирован, злоумышленник может получить трафик из всех VLAN. Злоумышленник теперь может прослушивать трафик из всех VLAN.

**Двойная маркировка**

Данный вид атаки основан на использовании принципов работы аппаратного обеспечения на большинстве коммутаторов. Большинство коммутаторов выполняют лишь один уровень деинкапсуляции 802.1Q, что позволяет злоумышленнику вставлять в кадр скрытую метку 802.1Q. Метка позволяет пересылать кадр в сеть VLAN, которая не указана первоначальной меткой 802.1Q. Важное свойство атаки с двойной инкапсуляцией заключается в том, что она действует, даже если транковые порты отключены, поскольку обычно узел отправляет кадр в сегменте, который не является транковым каналом.

Методы защиты
- Убедитесь, что порты не настроены на автоматическую работу в режиме транка
- Отключите протокол DTP
- НИКОГДА не используйте VLAN 1
- Отключите и перенесите в неиспользуемый VLAN неиспользуемые порты
- Всегда используйте специальный VLAN ID для всех транковых портов

## Защита от MAC-spoofing

Суть данной атаки заключается в подмене MAC-адреса на сетевой карте компьютера, что позволяет ему перехватывать пакеты, адресованные другому устройству, находящемуся в том же широковещательном домене.

В таблице MAC-адресов коммутатора запись с атакованным MAC-адресом будет соотнесена с интерфейсом, на котором в последний раз был идентифицирован кадр с данным source MAC. Таким образом, до поступления кадра с атакуемого устройства, все данные коммутатор, на основании своей CAM-таблицы, будет пересылать на атакующий компьютер.

Методы защиты

Официально рекомендуемый метод – включение port-security на интерфейсе коммутатора, например:
```
SW(config-if)#switchport mode access
SW(config-if)#switchport port-security
SW(config-if)#switchport port-security mac-address 0000.1111.1111

```
