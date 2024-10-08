# OSI

OSI (Open Systems Interconnection, Модель взаимодействия открытых систем) – модель, принятая в качестве стандарта ISO в 1983 году, используемая для понимания и описания взаимодействия различных сетевых протоколов. OSI делит сетевые функции на семь уровней.

PDU (Protocol Data Unit) - блок данных протокола.

# 1 Физический уровень

PDU: Бит.

Физическое соединение между устройствами, включая кабели и оборудование. Предназначен для передачи битов по физическому каналу связи. Канал связи имеет пропускную способность, задержку и количество ошибок, что определяет скорость передачи. Канал связи может быть симплексным, дуплексным и полудуплексным. Протоколы: Bluetooth, IRDA (Инфракрасная связь), медные провода (витая пара, телефонная линия), Wi-Fi.

На первом уровне модели OSI происходит передача физических сигналов от источника к получателю. Данные в современном мире можно передать разными способами, поэтому в стандарте описано несколько разных сред – передачи данных с помощью электрического тока, с помощью света, с помощью радиоволн.

На этом уровне оперируем кабелями, модуляцией сигнала в провод, контактами в разъемах, кодированием единиц и нулей.

Функции:
- Установление и разъединение физических соединений
- Передача и приём сигналов в последовательном коде
- Прослушивание каналов
- Идентификация каналов
- Оповещение о появлении неисправностей и отказов

# 2 Канальный уровень

PDU: Кадр.

Управляет связью между соседними устройствами в одной сети. Предназначен для превращения битов в кадры, присвоения физических MAC-адресов, проверки и исправления данных, передачи сообщений по каналу связи. На этом уровне работают коммутаторы и концентраторы. Используется протокол ARP. Например: на компьютере есть сетевая карта, для взаимодействия с ней есть драйвер.

Кадры - это логически организованная структура, в которую можно помещать данные. Полученные с физического уровня данные, представленные в битах, он упаковывает в кадры, проверяет их на целостность и, если нужно, исправляет ошибки (либо формирует повторный запрос повреждённого кадра) и отправляет на сетевой уровень.

Здесь также появляются такие понятия, как MAC-адрес, коммутатор, сеть Ethernet.

Функции:
- Организация (установление, управление, расторжение) канальных соединений и идентификация их портов
- Организация и передача кадров
- Обнаружение и исправление ошибок
- Управление потоками данных
- Обеспечение прозрачности логических каналов (передачи по ним данных, закодированных любым способом)

# 3 Сетевой уровень

PDU: IP-пакет.

Определяет лучший маршрут для пакетов данных и управляет IP-адресами. На этом уровне работают маршрутизаторы, которые используют протоколы RIP, EIGRP, OSPF. Объединяет сети, построенные на основе разных технологий (Ethernet, Wi-Fi, 3G, 4G, 5G, MPLS, ATM, FDDI).

Сетевой уровень устанавливает связь в вычислительной сети между двумя системами и обеспечивает прокладку виртуальных каналов между ними.

Сетевой уровень сообщает транспортному уровню о появляющихся ошибках. В передаваемых сообщениях помещаются фрагменты данных, а также логические адреса источника и назначения. Сетевой уровень отвечает за их адресацию и доставку.

Выполняет функции:
- Обнаружение и исправление ошибок, возникающих при передаче через коммуникационную сеть
- Упорядочение последовательностей пакетов
- Маршрутизация и коммутация
- Сегментирование и объединение пакетов

# 4 Транспортный уровень

PDU: Сегмент.

Отвечает за координацию передачи данных между конечными системами и хостами, включая количество данных для отправки, скорость, место назначения и так далее.

Определяет, следует ли устанавливать соединение, как проверить целостность данных, как восстановится после ошибки соединения и необходима ли повторная передача.

На этом уровне есть два основных протокола – TCP (Transmission Control Protocol) и UDP (User Datagram Protocol). Разница между ними в том, что различный транспорт применяется для разной категории трафика:

TCP обеспечивает контроль за передачей данных. Пример такого трафика – удаленное подключение к серверу. Так как с помощью удаленного подключения мы отправляем различные команды на сервер, потеря буквы в команде или даже целой команды может стать причиной ошибки и выхода сервера из строя.

UDP не обеспечивает контроль за передачей данных. Пример – видеоконференции. Потеря небольшого куска данных не сильно скажется на картинке, но зато большой объем видеопотока будет быстрее добираться до нашего устройства.

В случае использования TCP протокола устройство, отправляющее трафик, сначала устанавливает соединение с принимающей стороной. Происходит так называемый TCP-handshake (рукопожатие сторон), в котором устройства договариваются, что будут пересылать данные друг другу.

Если мы используем UDP, то никаких договоренностей нам не нужно - устройство просто отправит данные на приёмник вне зависимости от того, готова ли принимающая сторона обработать наши данные.

Функции:
- Управление передачей по сети и обеспечение целостности блоков данных.
- Обнаружение ошибок, частичная их ликвидация и сообщение о неисправленных ошибках.
- Восстановление передачи после отказов и неисправностей.
- Укрупнение или разделение блоков данных. Предоставление приоритетов при передаче блоков.
- Подтверждение передачи.
- Ликвидация блоков при тупиковых ситуациях в сети

# 5 Сеансовый уровень

PDU: Данные.

Устанавливает, поддерживает и разрывает соединения между приложениями на разных устройствах. Например: аудио и видеоконференции.

Сеансовый уровень обеспечивает управление диалогом для того, чтобы фиксировать, какая из сторон является активной в настоящий момент, а также предоставляет средства синхронизации.

Функции:
- Установление и завершение на сеансовом уровне соединения между взаимодействующими системами
- Выполнение нормального и срочного обмена данными между прикладными процессами
- Управление взаимодействием прикладных процессов
- Синхронизация сеансовых соединений
- Извещение прикладных процессов об исключительных ситуациях

# 6 Уровень представления

PDU: Данные.

На уровне представления выполняется шифрование и дешифрование данных, благодаря которым секретность обмена данными обеспечивается сразу для всех прикладных сервисов.

Этот уровень обеспечивает то, что информация передаваемая прикладным уровнем, будет понятна прикладному уровню в другой системе. В случаях необходимости уровень представления в момент передачи информации выполняет преобразование форматов данных в некоторый общий формат представления, а в момент приема, соответственно, выполняет обратное преобразование. Таким образом, прикладные уровни могут преодолеть, например, синтаксические различия в представлении данных.

Функции:
- Генерация запросов на установление сеансов взаимодействия прикладных процессов
- Согласование представления данных между прикладными процессами
- Реализация форм представления данных
- Представление графического материала
- Засекречивание данных
- Передача запросов на прекращение сеансов

# 7 Прикладной уровень

PDU: Данные.

Предоставляет пользовательский интерфейс, с которым взаимодействуют приложения. Например: просмотр веб-страниц, передача и прием почты, прием и получение файлов, удаленный доступ и тд.

Функции:
- Идентификация пользователей по их паролям
- Определение функционирующих
- Определение достаточности имеющихся ресурсов
- Организация запросов на соединение с другими прикладными процессами
