# TELNET

Telnet - протокол для реализации текстового терминального интерфейса по сети. Для установки соединения использует **порт 23**

Telnet был разработан в 1969 году, как универсальный инструмент доступа к командной строке удаленных сетевых устройств. В большинстве используемых операционных систем, начиная с середины 1970-х годов, Telnet использовался для организации удаленного доступа, из-за чего стал референсом для более поздних протоколов удаленного подключения (ssh, ftp, rdp).

Для установки соединения Telnet использует протокол TCP, c подключением через 23 порт.

## Принципы

Основу протокола составляют три базовые концепции:

1. Когда устанавливается соединение, предполагается, что оно начинается и завершается на "Сетевом Виртуальном Терминале" (Network Virtual Terminal, NVT). NVT -- это воображаемое устройство, которое создает промежуточное стандартное представление канонического терминала. NVT является стандартным описанием наиболее широко используемых возможностей реальных физических терминальных устройств. NVT позволяет описать и преобразовать в стандартную форму способы отображения и ввода информации. Терминальная программа и работающий с ней процесс преобразовывают характеристики физических устройств в спецификацию NVT, что позволяет, с одной стороны, унифицировать характеристики физических устройств, а с другой обеспечить совместимость устройств с различными возможностями.
2. Используя согласование параметров, терминальная программа предлагает обслуживающему процессу использовать управляющие последовательности для управления выводом информации. Получив такую команду процесс начинает вставлять управляющие последовательности в данные, предназначенные для отображения. Протокол предлагает структуру дополнительных параметров "DO, DON'T, WILL, WON'T", позволяющую пользователю и серверу более точно договориться об используемых в соединении соглашениях. Команда WILL XXX указывает на предложение отправляющей стороны использовать параметр XXX. DO XXX и DON'T XXX являются, соответственно, положительным и отрицательным ответом. Аналогично, DO XXX посылается как предложение получающей стороне использовать параметр XXX. WILL XXX и WON'T XXX суть положительный и отрицательный ответы. Так как NVT это то, что остается, когда никакие параметры не используются, ответы DON'T и WON'T гарантируют, что соединение останется в состоянии, которое подходит обеим сторонам.
3. Симметрия терминалов и процессов отражает тот факт, что все управляющие команды протокола могут даваться любой стороной, участвующей в соединении. Разделения на "клиент" и "сервер" здесь нет.

## Режимы функционирования

Существуют четыре режима, в которых функционирует большинство Telnet клиентов и серверов:

Первый – **полудуплексный**. NVT (Сетевой виртуальный терминал) по умолчанию это полудуплексное устройство, которое требует исполнения команды GO AHEAD (GA) от сервера, перед тем как будет принят ввод от пользователя. Ввод пользователя отображается локальным эхом от NVT клавиатуры на NVT принтер, таким образом, от клиента к серверу посылаются только полные строки.

Второй – **символ за один раз**. Это самый распространенный режим, где каждый вводимый символ отправляется серверу отдельно от других. Сервер отвечает клиенту сигналом эхо (опция ECHO стандартизована в RFC 857), что позволяет большинству вводимых символов отражаться на экране клиента. Эта методика увеличивает объем трафика, курсирующего по сети между клиентом и сервером telnet.

Третий режим – **строка за один раз**. Если какая-либо опций (ECHO и SUPPRESS GO AHEAD) не включена, Telnet находится в режиме строка за один раз. В этом режиме строка текста редактируется на локальном компьютере и отправляется только тогда, когда она будет полностью готова. На такая возможность есть не всегда и не у всех сервисов;

И четвертый режим – **линейный режим** (linemode).В данном случае этот термин означает реальную опцию linemode (RFC 1184). Когда LINEMODE включен, обработка символов выполняется в локальной системе под управлением удаленной системы. Когда редактирование ввода или отображение символов должны быть отключены, удаленная система будет передавать эту информацию. Удаленная система также будет передавать изменения любым специальным символам, которые происходят в удаленной системе, чтобы они могли действовать в локальной системе.

## Безопасность

При первоначальной разработке telnet в 1969 году большинство пользователей подключенных к сети компьютеров или находились в компьютерных отделениях академических учреждений, или являлись частью правительственных/частных исследований. Поэтому вопросы безопасности вставали не так остро, как это стало позднее, в 1990-х. Резко выросло людей, имеющих доступ к Интернету, а значит и количество людей, пытающихся взломать чужие сервера. Это вызвало необходимость в зашифрованных альтернативах.

Эксперты компьютерной безопасности рекомендовали, чтобы использование Telnet для удалённых входов в систему было прекращено во всех нормальных обстоятельствах  по следующим причинам:

- Telnet по умолчанию не шифрует оправленные данные (включая и пароли), а значит очень легко подслушать канал и узнать пароли. Для этого достаточно иметь доступ к маршрутизатору, свитчу, хабу или шлюзу, расположенных между двумя использующимися узлами Telnet, и любой имеющий к ним доступ может прервать передачу пакетов, получить логин и пароль вне зависимости от анализатора пакетов.
- Во многих реализациях Telnet нет аутентификации, которая обеспечила бы гарантию передачи пакетов и отсутствия вторжения посередине.
- Обнаружено несколько уязвимостей демонов, который обычно использует Telnet

Эти связанные с безопасностью недостатки наглядно показывали, что использование протокола Telnet в общедоступной сети опасно и не оптимально