# UDP

UDP (User Datagram Protocol) - протокол дейтаграмм пользователя, который обеспечивает передачу данных без получения подтверждения от пользователя, благодаря чему достигается большая скорость работы и передачи данных в ущерб надежности и безопасности. Также не доступна повторная передача потерянных пакетов и данные доставляются вразнобой.

Протоколы, которые работают на UDP: DNS, SNMP, DHCP.

С помощью UDP компьютерные приложения могут посылать сообщения другим хостам по IP-сети без необходимости предварительного сообщения для установки специальных каналов передачи или путей данных. Такие сообщения называются датаграммы. Этот протокол работает намного быстрее, чем TCP, но в нем нет механизма повторной передачи данных, поэтому такой протокол считается ненадежным.

## Структура пакета

![](materials/images/networks/udp_packet.png)

- Порт отправителя и порт получателя - как и в случае с TCP, в заголовке будет находится цифра порта, куда трафик адресован, а также порт, откуда трафик отправлялся. Порт получателя нужен для того, чтобы принимающее устройство могло определить, какой программе направить трафик на обработку, ведь одновременно на компьютере может работать большое количество программ. Порт отправителя содержит значение порта, на который при необходимости будет посылаться ответ в случае необходимости
- Длина датаграммы - поле, задающее длину всей дейтаграммы (заголовка и данных) в байтах. Минимальная длина равна длине заголовка - 8 байт. Теоретически, максимальный размер поля - 65535 байт для UDP-дейтаграммы (8 байт на заголовок и 65527 на данные). Фактический предел для длины данных при использовании IPv4 - 65507 (помимо 8 байт на UDP-заголовок требуется еще 20 на IP-заголовок)
- Контрольная сумма - поле контрольной суммы используется для проверки заголовка и данных на ошибки. Если сумма не сгенерирована передатчиком, то поле заполняется нулями

## Принцип работы

UDP получает сообщения от прикладного уровня, добавляет к ним поля номеров портов отправителя и получателя для демультиплексирования приемной стороной, а также два других специальных поля и передает полученный сегмент сетевому уровню. Сетевой уровень заключает сегмент в дейтаграмму и «по возможности» передает ее хосту назначения. Если последний успешно получает сегмент, протокол UDP с помощью поля номера порта получателя направляет данные сегмента нужному процессу. Обратите внимание на то, что протокол UDP не предусматривает процедуры рукопожатия перед началом передачи сегментов. Поэтому говорят, что UDP осуществляет передачу данных без установления соединения.

## Сравнение TCP и UDP

TCP
- Ориентирован на соединение
- Подходит для приложений, требующих высокой надёжности, где время передачи менее критично
- Упорядочивает пакеты данных в указанном порядке
- Гарантирует, что переданные данные останутся нетронутыми. Если пакет повреждён по прибытии, он отправляется повторно
- Ошибки выявляются по контрольной сумме
- Относительно медленный
- Размер заголовка TCP составляет 20 байт. Данные считываются как поток байтов, и никакие чёткие указания не передаются границам сегмента

UDP
- Без установления соединения
- Подходит для приложений, которым требуется быстрая и эффективная передача. Полезно для серверов, которые отвечают на небольшие запросы от большого количества клиентов
- Пакеты могут приходить не по порядку, UDP их не упорядочивает
- Нет никакой гарантии, что отправленные сообщения или пакеты достигнут места назначения
- UDP выполняет проверку ошибок, но просто отбрасывает пакеты. Исправление ошибок не предпринимается
- Относительно быстрый
- Размер заголовка UDP составляет 8 байт. Пакеты отправляются индивидуально и проверяются на целостность только в том случае, если они приходят
