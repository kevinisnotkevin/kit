# SS

**ss (статистика сокетов)** - мощная утилита командной строки для исследования сокетов. Он сбрасывает статистику сокетов и отображает информацию, аналогичную netstat. Кроме того, он показывает больше информации о TCP и состоянии по сравнению с другими аналогичными утилитами.

```bash
# Вывод список всех открытых TCP-портов
ss -ta

# Вывод всех активных TCP-соединений вместе с их таймерами
ss -to
```

Опции:
    - **V** — Version показать версию утилиты
    - **n** — Numeric не определять имена служб
    - **r** — Resolve определять сетевые имена адресов с помощью dns
    - **a** — All отобразить все сокеты (открытые соединения)
    - **l** — Listening показать только прослушиваемые сокеты
    - **o** — Options показать информацию таймера
    - **e** — Extended выводить расширенную информацию о сокете
    - **p** — Processes, показать процессы, использующие сокет
    - **i —** Internal, посмотреть внутреннюю информацию TCP
    - **s** — Summary, статистика использования сокета
    - **D** — экспортировать текущее состояние TCP сокетов в файл
    - **F** — работать с информацией, взятой из файла
    - **4, —ipv4** — только сокеты протокола IP версии 4
    - **6 —ipv6** — только сокеты протокола IP версии 6
    - **0, —packet** — только PACKET сокеты
    - **t, —tcp** — TCP сокеты
    - **u, —udp** — UDP сокеты
    - **d, —dhcp** — DHCP сокеты
    - **r, —raw** — RAW сокеты
    - **x, —unix** — UNIX сокеты