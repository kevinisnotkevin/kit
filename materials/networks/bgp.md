# BGP

Border Gateway Protocol - это основной протокол динамической маршрутизации, который используется в сети Интернет. Сеть интернет – весьма сложное объединение сетей провайдеров и организаций. Но их взаимодействие как раз обеспечивает доступ ко всем ресурсам глобальной сети. Сетевые кабели соединяют между собой провайдерские сети, позволяя обмениваться информацией межконтинентально. А на суше таких кабелей ещё больше – они соединяют как провайдеров, так и организации.

Каждый провайдер или организация имеет под управлением набор ip сетей, информацию меж которыми надо маршрутизировать. Этим и занимается протокол BGP в глобальном смысле.

Так как на протоколе завязано все взаимодействие в глобальной сети, требования к нему соответствующие. В нем присутствует большое количество терминов, которые мы разберем по мере прохождения занятия с целью разобраться, как этот протокол работает.

Итак:

**Внутренний протокол маршрутизации (interior gateway protocol)** – протокол, который используется для передачи информации о маршрутах внутри автономной системы.

**Внешний протокол маршрутизации (exterior gateway protocol)** – протокол, который используется для передачи информации о маршрутах между автономными системами.

**Автономная система (autonomous system, AS)** — набор маршрутизаторов, имеющих единые правила маршрутизации, управляемых одной технической администрацией и работающих на одном из протоколов IGP (для внутренней маршрутизации AS может использовать и несколько IGP).

**Транзитная автономная система (transit AS**) — автономная система, через которую передается трафик других автономных систем.

**Путь (path)** — последовательность, состоящая из номеров автономных систем, через которые нужно пройти для достижения сети назначения.

**Атрибуты пути (path attributes, PA)** — характеристики пути, которые позволяют выбрать лучший путь.

**BGP speaker** — маршрутизатор, на котором работает протокол BGP.

**Соседи (neighbor, peer)** — любые два маршрутизатора, между которыми открыто TCP-соединение для обмена информацией о маршрутизации.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/798789ad-4b55-48f1-b123-6e01faf398f1/8b21872e-7443-41c3-8e3c-c6e265f562f7/Untitled.png)

На рисунке можно увидеть тот самый AS path. Чтобы добраться из автономной системы 200 в AS 300, нужно пройти через сотую AS. При таком взаимодействии она будет называться транзитной

Еще на данном рисунке можно увидеть роутеры, являющиеся соседями BGP. Они выделены красным и зеленым цветами.

BGP выбирает лучшие маршруты не на основании технических характеристик пути (например, пропускной способности и задержки), а на основании политик. В локальных сетях наибольшее значение имеет скорость сходимости сети, время реагирования на изменения. И маршрутизаторы, которые используют внутренние протоколы динамической маршрутизации, при выборе маршрута, как правило, сравнивают какие-то технические характеристики пути, например, пропускную способность соединений.

При выборе между каналами двух провайдеров, зачастую имеет значение не то, у какого канала лучше технические характеристики, а определенные внутренние правила компании. Например, использование какого канала обходится компании дешевле. Поэтому в BGP выбор лучшего маршрута осуществляется на основании политик, которые настраиваются с использованием фильтров, анонсирования маршрутов и изменения атрибутов.

Протокол BGP бывает двух видов – Внутренний BGP (Internal BGP) и Внешний BGP (External BGP). Это ранее мы обозначили.

Отличие IBGP от EBGP состоит в том, что при объявлении маршрута BGP-соседу, находящемуся в той же самой АС, маршрутизатор не должен добавлять в AS PATH номер своей автономной системы, иначе произойдет петля маршрутизации.

**Общие характеристики**

- Использует TCP для передачи данных, что обеспечивает надежную доставку обновлений протокола
- Отправляет обновления только после изменений в сети
- Периодически отправляет keepalive-сообщения для проверки соединения
- Метрика протокола называется path vector или атрибуты

**Сообщения BGP**

Для обеспечения взаимодействия роутеров по протоколу BGP используется набор сообщений.

После установки соединения по TCP роутер отправляет сообщение OPEN. Такое сообщение отсылается только в самом начале BGP-сессии для согласования параметров. В нём передаются версия протокола, номер AS, Hold Timer и Router ID.

- номер версии – предлагаемый номер версии протокола. Наибольший номер версии, поддерживаемый маршрутизаторами. Большинство реализаций BGP маршрутизации сейчас используют BGP4.
- номер AS – номер автономной системы локального маршрутизатора. Соседний маршрутизатор проверяет эту информацию. Если получен некорректный номер AS (например, тот же, что используется и у другого роутера), то BGP сессия заканчивается.
- hold timer – максимальное число секунд, которое может пройти между успешными keepalive или update сообщениями отправителя, чтобы поддерживать bgp сессию. При получении open сообщения маршрутизатор вычисляет значение hold таймера путем использования меньшего значения между настроенным на маршрутизаторе и полученным в open сообщении
- BGP router-ID – 32-битное поле, показывающее BGP идентификатор отправителя. BGP ID – это IP адрес, назначенный маршрутизатору, он определяется при загрузке.
- дополнительные параметры – различные параметры, определенные в стандарте BGP, например, аутентификация сессии.

Получив OPEN от первого роутера, второй отправляет свой OPEN, а также KEEPALIVE, говорящий о том, что OPEN от первого получен – это сигнал для первого роутера переходить к следующему состоянию – Established. Это состояние означает, что запущена правильная версия BGP и все настройки консистентны.В каждом роутере можно посмотреть Uptime – как долго он находится в состоянии ESTABLISHED с соседями.

В первые мгновения после установки BGP-сессии в таблице BGP только информация о локальных маршрутах. Теперь переходим к обмену маршрутной информацией.

Для это используются сообщения UPDATE. Каждое сообщение UPDATE может нести информацию об одном новом маршруте или о удалении группы старых. Причём одновременно. UPDATE передаются при каждом изменении в сети пока длится BGP-сессия.

Рассмотрим подробно, какая информация передается в UPDATE:

- withdrawn routes – список отображает IP адреса префиксов маршрутов, которые выведены из эксплуатации, если таковые имеются
- path attributes – эти атрибуты включают: путь к автономной системе, происхождение маршрута, локальные политики.
- network-layer reachability information – это поле содержит список префиксов IP адресов, которые доступны по этому пути.

Тут стоит пояснить, что термин префикс – это по сути адрес сети с указанием маски подсети.

На каждый UPDATE роутер отвечает KEEPALIVE для подтверждения, что информация получена.

При этом, даже если обновлений маршрутов не происходило, каждый BGP-маршрутизатор регулярно будет рассылать сообщения KEEPALIVE для подтверждения, что роутер ещё работает. Это происходит с истечением таймера Keepalive – по умолчанию 60 секунд.

**Правила анонсирования маршрутов**

При выборе маршрута, который должен быть отправлен соседу в сообщении Update, BGP использует такие правила:

- Отправлять только лучшие маршруты из таблицы BGP
- Для iBGP-соседей — не анонсировать маршруты, которые были выучены от других iBGP-соседей
- Для eBGP-соседей — не анонсировать маршруты, для которых номер автономной системы соседа уже находится в атрибуте AS path
- Не анонсировать маршруты, которые отфильтрованы настройками

**Команда Redistribute**

Чтобы подсети в автономной системе смогли взаимодействовать с внешним миром, протокола BGP будет мало, ведь внутри AS используется IGP протокол маршрутизации, который зачастую является динамическим. Чтобы постоянно не перенастраивать правила анонсирования маршрутов через BGP, может использоваться настройка redistribute.

Такая настройка сообщает протоколу, что необходимо по определённым правилам взять маршруты из других динамических протоколов маршрутизации и анонсировать их соседям. На самом деле это не обязательно должны быть именно динамические маршруты – перераспределять в bgp можно и статические, и непосредственно подсоединенные маршруты, все зависит от задач сетевого администратора.