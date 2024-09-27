# Логические порты

Логический порт - номер, присвоенный логическому соединению.

Существует 65536 портов TCP и UDP, разделённых на три основные группы следующим образом:

**Известные порты - 0-1023.** Номера портов в диапазоне “известных” портов назначаются наиболее часто используемым и важным протоколам. Номера портов являются постоянными.

**Зарегистрированные порты - 1024-49151.** IANA назначила эти части IT-компаниям, которые хотят зарегистрировать их для проприетарных приложений. Например, Microsoft RDP – 3389 и Dropbox – 7600.

**Динамические и частные порты - 49152-65535.** Эта группа охватывает оставшиеся логические порты, доступные для любого приложения.

Диапазон портов известен как “клиентские порты” и обычно используется для логического подключения клиента.

Например, хост, подключающийся к веб-серверу, отправляет запросы на порт 80. Это позволяет веб-серверу узнать, какую службу запрашивает клиент. Однако обратный трафик должен быть отправлен на исходный порт, который выбирается случайным образом. Когда соединение закрывается, порт можно повторно использовать для другого соединения, что позволяет клиенту устанавливать несколько соединений и управлять ответами из нескольких источников.

## Основные порты

Популярные номера портов TCP и UDP, и протоколы, работающие на них.

**TCP:**
- 80 порт - HTTP
- 443 порт - HTTPS
- 25, 465 порты - SMTP
- 110 порт - POP3
- 22 порт - SSH
- 3389 порт - RDP
- 445 порт - SMB
- 20, 21 порты - FTP

**UDP:**
- 53 порт - DNS
- 67, 68 порты - DHCP
- 69 порт - TFTP
- 123 порт - NTP