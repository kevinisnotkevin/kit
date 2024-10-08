# WAF

## Что такое WAF?

Web Application Firewall (WAF) - средства фильтрации трафика прикладного уровня, специально ориентированные на веб-приложения. Основное предназначение WAF - защита веб-приложения от несанкционированного доступа, даже при наличии критичных уязвимостей. Применение Web Application Firewall традиционно считается наиболее эффективным подходом к защите веб-ресурсов. WAF может быть реализован как облачный сервис, агент на веб-сервере или специализированное железное или виртуальное устройство.

С момента создания локальных и интернет-сетей появилась необходимость в защите пользовательских ПК и серверов от возможного взлома. Изначально это был Firewall - сетевой экран, отсеивающий «подозрительный» трафик (с «серых» IP-адресов). Такая защита примитивная и совсем не подходит для веб-приложений, где взлом может осуществляться даже через «белый» IP. На смену сетевым экранам в дальнейшем пришли:

- Пакетные фильтры сканировали входящий трафик и при обнаружении подозрительного набора пакетов - закрывали соединение.
- IDS/IPS - это одновременная защита через пакетные фильтры и с «прослушиванием» TCP-портов. И в IDS/IPS отслеживается активность не только извне, но и внутри локальной сети. Главный недостаток: большое количество ложных срабатываний, особенно когда интернет-сессия между пользователем и сервером задействует множество портов TCP.
- UTM - условно считается модификацией IDS/IPS, но с добавлением дополнительного сетевого экрана (используется как антивирус, прокси, балансировщик и так далее). В сравнении с IDS/IPS - сортирует TCP-порты по сессиям.

Но и UTM оказалось недостаточно. Простой пример: если пользователь одновременно в браузере откроет веб-приложение банка и вкладку с java-скриптом, направленным на инициализацию отправки денег на определенную банковскую карту, то данный процесс не будет предотвращен ни антивирусом, ни самим браузером, ни сетевым экраном, так как весь трафик будет сымитирован как обычные действия пользователя.

А Web Application Firewall полностью анализирует трафик и защищает веб-приложения от несанкционированных действий и подозрительных атак. Ключевые отличия WAF:

- применяется машинное самообучение (на основе пользовательских действий).
- интегрированный сканер потенциальных уязвимостей.
- поддержка корреляции сетевых атак (даже если задействуется одновременно несколько портов на разных протоколах), что существенно упрощает дальнейший анализ журналов.

## Архитектура WAF

Для эффективного обнаружения различных способов обхода WAF, специалисту необходимо разобраться в современных механизмах классификации запросов. Каждый WAF индивидуален и имеет уникальное внутреннее устройство, однако существуют некоторые типовые методы, применяемые для анализа. Давайте их и рассмотрим.

### Правила на основе регулярных выражений

Большинство существующих WAF базируется на правилах, основанных на регулярных выражениях. Для их создания некоторое известное множество атак изучается разработчиком WAF, в результате определяются ключевые синтаксические конструкции, по наличию которых можно утверждать о проведении атаки. На основе полученных результатов пишутся регулярные выражения, способные находить такие конструкции. Кажется, что всё просто, однако такой подход имеет ряд недостатков. Зона применимости регулярного выражения ограничивается одним запросом, а чаще даже конкретным параметром запроса, что очевидно снижает эффективность таких правил и создает ряд «слепых зон» для подобного механизма. Во-вторых, синтаксис регулярных выражений, сложная логика текстовых протоколов, допускающая замену на эквивалентные конструкции и использование различных представлений символов, приводят к ошибкам при создании подобных правил.

```
(?i)(<script[^>]*>.*?)
```
- Это выражение ищет HTML-инъекцию типа XSS в теле запроса. Первая часть (`(?i)`) делает последующую часть выражения нечувствительной к регистру, вторая (во вторых скобках) ищет открывающийся тег `<script` с произвольными параметрами внутри тега и произвольный текст после символа `>`.

### Scorebuilding

Данный механизм должен быть знаком всем, кто интересовался устройством межсетевых экранов и антивирусов. Сам по себе он не детектирует атаки, однако дополняет другие методы, делая их более точными и гибкими. Причина появления инструмента - в том, что присутствие в запросе некоторой «подозрительной» конструкции является недостаточным условием для выявления атаки или же, напротив, может приводить к большому числу false-positive ошибок. Данная проблема решается введением балльной системы. Например, каждое правило, основанное на регулярных выражениях, дополняется информацией о критичности его срабатывания; после выявления всех сработавших правил их критичность суммируется. В случае преодоления некоторого порогового значения атака детектируется, а запрос блокируется. Данный механизм, несмотря на свою простоту, хорошо зарекомендовал себя в задачах подобного класса и применяется повсеместно.

### Токенайзеры

Этот подход к детектированию атак был представлен на Black Hat 2012 в виде C/C++ библиотеки libinjection, позволяющей быстро и точно выявлять атаки класса SQL injection. На данный момент, для библиотеки libinjection существуют порты под различные языки программирования, включая PHP, Lua, Python, etc… По сути, механизм сводится к поиску сигнатур, представленных в виде последовательности токенов. Некоторое количество сигнатур вносится во встроенный черный список и считается недопустимым или вредоносным. Иными словами, перед тем, как проанализировать какой-либо запрос, его сначала приводят к набору токенов. Токены подразделяются на различные типы, например, variable, string, regular operator, unknown, number, comment, union-like operator, function, comma, etc… Один из основных недостатков данного метода заключается в том, что существует возможность построить такую конструкцию, которая приведет к некорректному формированию токенов, следовательно, сигнатура запроса будет отлична от ожидаемой. Такие конструкции обычно называются token breaker.

### Анализ поведения

Детектирование попыток эксплуатации уязвимостей в параметрах запроса - не единственная задача WAF. Важно выявлять и саму процедуру поиска уязвимости, которая может проявляться в попытках сканирования, брутфорса директорий, фаззинга параметров и прочих часто применяемых автоматизированными средствами методик обнаружения уязвимостей и соответственным образом реагировать на них. Более продвинутые WAF даже умеют строить цепочки запросов, «типичные» для нормального поведения пользователя и блокировать попытки отправки запросов в отличном от стандартного поведения порядке. Данный механизм не столько противодействует атакам, сколько затрудняет процесс нахождения уязвимости. Ограничение на количество запросов в минуту никак не скажется на типичном пользователе, но будет существенной помехой для сканера, работающего в несколько потоков.

### Репутационный анализ

Еще один механизм, напрямую унаследованный от межсетевых экранов и антивирусов. Сегодня почти любой WAF включает в себя списки адресов VPN-сервисов, анонимайзеров, узлов Tor-сети, участников ботнетов, которые могут применяться для блокировки запросов, исходящих от подозрительных адресов. Более продвинутые WAF умеют автоматически обновлять свои базы и вносить в них дополнительные записи на основе анализируемого трафика.

### Машинное обучение

Один из самых спорных моментов в WAF. Данный механизм сложнее всего описать, и тому есть множество причин. Для начала, стоит отметить, что само понятие машинное обучение крайне обширно и фактически включает в себя множество технологий и методик. Кроме того, машинное обучение считается лишь одним из классов методов ИИ. Внедрение машинного обучения или “использование ИИ” - очень популярный маркетинговый ход. Зачастую непонятно, какие методы и алгоритмы используются на практике, а иногда со стороны атакующего кажется, что машинное обучение в WAF - это лишь красивые слова. Среди тех игроков рынка, кто действительно смог подчинить себе всю мощь машинного обучения, мало кто готов делиться своим опытом. Всё это складывается в довольно печальную картину для «человека извне», желающего разобраться в вопросе. И всё же попробуем выделить хотя бы парочку здравых идей на основе доступной информации.

Во-первых, машинное обучение полностью зависит от тех данных, на основе которых оно осуществлялось, и это зачастую является большой проблемой. Требуется наличие у компании-разработчика актуальной и полной коллекции существующих атак и их методов применения, что довольно непросто. По этой причине многие поставщики тщательно логируют результаты работы своих WAF и сотрудничают с другими компаниями, предлагающими IDS, SIEM системы для доступа к реальным примерам атак. Во-вторых, модель, обученная на «сферическом веб-приложении в вакууме» может оказаться попросту неэффективной при установке на реальное веб-приложение клиента. Для наилучшего эффекта считается правильным проводить дополнительное обучение модели на стадии внедрения WAF у клиента, что требует дополнительных расходов, времени, организационных трудностей, а также не гарантирует лучшего результата.

## Принципы работы WAF

Классическое размещение WAF в сети - в режиме обратного прокси-сервера, перед защищаемыми веб-cерверами. В зависимости от производителя могут поддерживаться и другие режимы работы - например, прозрачный прокси-сервер, мост или даже пассивный режим, когда продукт работает с репликацией трафика.

Работает WAF по следующим моделям безопасности:

- **Negative**. Некий «черный список», запрещающий прием конкретной информации, прописанной в настройках. Защищает веб-приложения на прикладном уровне (аналог IPS), но умеет оценивать потенциальные угрозы детальнее и чаще применяется для обеспечения защиты от «популярных» и специфических типов атак. Анализирует уязвимости конкретных веб-приложений. Пример работы Negative: запрещать заранее заданный «плохой» запрос GET по HTTP и разрешать все остальное.
- **Positive**. «Белый список», разрешающий прием конкретной информации, которая была заранее указана в настройках. Позволяет получить максимальную защиту, т.к. применяется в качестве дополнения к моделям. Задействует другой тип логики: правила, определяющие, что конкретно разрешено. Пример работы Positive: разрешать указанные ранее запросы GET по HTTP для заданного адреса и запрещать все остальное.

Давайте рассмотрим механизмы работы WAF изнутри. Этапы обработки входящего трафика в большинстве WAF одинаковы. Условно можно выделить пять этапов:

1. Парсинг HTTP-пакета, который пришел от клиента.
2. Выбор правил в зависимости от типа входящего параметра.
3. Нормализация данных до вида, пригодного для анализа.
4. Применение правила детектирования.
5. Вынесение решения о вредоносности пакета. На этом этапе WAF либо обрывает соединение, либо пропускает дальше - на уровень приложения.

Все этапы, кроме четвертого, хорошо изучены и в большинстве файрволов одинаковы. О четвертом пункте - правилах детектирования - дальше и пойдет речь. Если проанализировать виды логик обнаружения атак в пятнадцати наиболее популярных WAF, то лидировать будут:

- регулярные выражения;
- токенайзеры, лексические анализаторы;
- репутация;
- выявление аномалий;
- score builder.

Большинство WAF используют именно механизмы регулярных выражений («регэкспы») для поиска атак. На это есть две причины. Во-первых, так исторически сложилось, ведь именно регулярные выражения использовал первый WAF, написанный в 1997 году. Вторая причина также вполне естественна - это простота подхода, используемого регулярками.

## Разновидности WAF

На рынке представлено множество WAF. Однако не все они одинаковы. Различные формы WAF имеют свои преимущества и недостатки, поэтому важно понимать их различия, прежде чем принимать взвешенное решение.

### Аппаратные WAF

WAF на аппаратной основе развертывается с помощью аппаратного устройства, устанавливаемого локально в сети LAN рядом с веб-серверами и серверами приложений. Операционная система работает внутри устройства, поддерживая конфигурацию и обновление программного обеспечения.

Главным преимуществом аппаратного WAF является высокая скорость и производительность. Благодаря физической близости к серверу, устройство отслеживает и фильтрует пакеты данных, поступающие на веб-сайт и обратно, с очень низкой задержкой. Основной недостаток заключается в том, что владение и обслуживание аппаратных машин обходится недешево. От приобретения и установки до хранения и обслуживания, аппаратные WAF связаны с более высокими затратами по сравнению с другими типами WAF.

Аппаратные WAF обычно используются крупными организациями, которые ежедневно получают сотни тысяч посещений. Это связано с тем, что для эффективного обслуживания такого большого количества клиентов скорость и производительность становятся наивысшим приоритетом. Кроме того, большинство крупных предприятий могут позволить себе расходы на управление и эсплуатацию аппаратного обеспечения.

### Программные WAF

Программный WAF устанавливается на виртуальной машине (VM) вместо физического аппаратного устройства. Все компоненты WAF по сути те же, что и в аппаратном WAF. Единственное отличие заключается в том, что для работы виртуальной машины пользователям потребуется собственный гипервизор. Основное преимущество WAF на базе программного обеспечения - гибкость. Его можно использовать не только в локальной системе, виртуальная машина также может быть развернута в облаке, подключаясь к облачным веб-серверам и серверам приложений. Программный WAF также является более дешевым вариантом по сравнению с аппаратными WAF. Однако основным недостатком является то, что поскольку он работает на виртуальной машине, в процессе мониторинга и фильтрации возникает большая задержка, что делает его менее быстрым, чем аппаратный WAF.

Очевидно, что программные WAF обычно используются в организациях с веб-серверами и серверами приложений, расположенными в облаке, таких как центры обработки данных и хостинг-провайдеры. Они также популярны среди малых и средних предприятий, которым необходимо защитить свои веб-приложения при меньших затратах.

### Облачные WAF

Облачный WAF - это новое поколение WAF, который предоставляется и управляется непосредственно поставщиком услуг в форме SaaS (software as a service - программное обеспечение как услуга). В отличие от программного WAF, компоненты WAF полностью находятся в облаке, поэтому пользователю не нужно ничего устанавливать локально или на виртуальных машинах.

Основным преимуществом является простота. Пользователю не нужно физически устанавливать какое-либо программное обеспечение, ему необходимо лишь подключиться к плану подписки. Поставщик услуг обеспечивает всю оптимизацию и обновления, так что пользователю не нужно самостоятельно управлять WAF. С другой стороны, недостатком является то, что поскольку WAF полностью управляется поставщиком услуг, у пользователя остается не так много возможностей для настройки.

Облачные WAF подходят для большинства малых и средних организаций, поскольку они не требуют физического места хранения и ручного обслуживания, они отлично подходят для организаций, не имеющих много дополнительных ресурсов для управления WAF.

## Применение WAF на практике

Web Application Firewall - защитный экран уровня приложений, предназначенный для выявления и блокирования современных атак на веб-приложения, в том числе и с использованием уязвимостей нулевого дня:

- SQL Injection — sql инъекции;
- Remote Code Execution (RCE) — удаленное выполнение кода;
- Cross Site Scripting (XSS) — межсайтовый скриптинг;
- Cross Site Request Forgery (CSRF) — межстайтовая подделка запросов;
- Remote File Inclusion (RFI) — удалённый инклуд;
- Local File Inclusion (LFI) — локальный инклуд;
- Auth Bypass — обход авторизации;
- Insecure Direct Object Reference — небезопасные прямые ссылки на объекты;
- Bruteforce — подбор паролей.

Основная его функция - детектирование и блокирование запросов, в которых, согласно анализу WAF, есть некоторые аномалии или прослеживается атакующий вектор. Такой анализ не должен затруднять взаимодействие легитимных пользователей с веб-приложением, в то же время должен точно и своевременно детектировать любые попытки атак. Для того, чтобы реализовать такую функциональность, разработчики WAF обычно применяют регулярные выражения, токенайзеры, поведенческий анализ, репутационный анализ, а также машинное обучение, и зачастую все эти технологии используются совместно. Кроме этого, WAF может еще предоставлять и другую функциональность: защита от DDoS, блокировка IP-адресов атакующих, отслеживание подозрительных IP-адресов, добавление security-заголовков (X-XSS-Protection, X-Frame-Options, etc…), добавление http-only флага к cookie, внедрение механизма HSTS, добавление функциональности CSRF-токенов. Также некоторые WAF имеют встраеваемый на сайт клиентский модуль, написанный на JavaScript.

Разумеется, WAF создает ряд трудностей для работы хакеров и пентестеров. Обнаружение и эксплуатация уязвимостей становится более трудоемкой задачей, если, конечно, атакующим не известны эффективные 0day-способы обхода конкретного WAF. Использовать автоматизированные сканеры при анализе веб-приложений под защитой WAF почти бесполезно. WAF надежно защищает сайты как минимум от scriptkiddies. Однако, опытный специалист или хакер, не имея должной мотивации, может также решить не тратить много времени на поиск способов обхода. Отдельно отметим, что, чем сложнее и многофункциональнее веб-приложение, тем больше возможная область атаки, и тем проще будет найти способ обхода WAF.

Общие требования к современному Web Application Firewall:

- системные компоненты WAF должны соответствовать требованиям PCI DSS;
- возможность реагирования на угрозы, описанные в OWASP Top 10;
- инспектирование запросов и ответов в соответствии с политикой безопасности, журналирование событий; предотвращение утечки данных — инспекция ответов сервера на наличие критичных данных;
- применение как позитивной, так и негативной модели безопасности; инспектирование всего содержимого веб-страниц, включая HTML, DHTML и CSS, а также нижележащих протоколов доставки содержимого (HTTP/HTTPS);
- инспектирование сообщений веб-сервиса, если веб-сервис подключен к интернету (SOAP, XML);
- инспектирование любого протокола или конструкции данных, использующихся для передачи данных веб-приложения вне зависимости от того, является ли он проприетарным или стандартизованным (как для входящих, так и исходящих потоков данных);
- защита от угроз, направленных непосредственно на WAF;
- поддержка SSL\TLS-терминации соединения;
- предотвращение или обнаружение подделки идентификатора сессии;
- автоматическое скачивание обновлений сигнатур атак и применение их;
- возможность установки режима fail-open и fail-close;
- поддержка устройством клиентских SSL-сертификатов;
- поддержка аппаратного хранения ключей (FIPS).