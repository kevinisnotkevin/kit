# XSS

## Практика
### Пример 1

Пример с reflected XSS. Веб-приложение принимает пользовательский ввод и выводит его на html страницу.
```
https://test.com?message=Ok.
<p>Message: Ok.</p>
```
В случае, если приложение не проверяет пользовательский ввод, то может быть внедрен вредоносный код.
```
https://test.com?message=<script>alert(1)</script>
<p>Message: <script>alert(1)</script></p>
```
Если пользователь посещает URL злоумышленника, то скрипт злоумышленника будет выполнен в веб-приложении в браузере пользователя в контексте сеанса пользователя. Скрипт может выполнить любые действия и получать доступ к любым данным, доступным пользователю.

### Пример 2
Пример stored XSS. Веб-приложение позволяет пользователям оставлять комментарии к блогу на странице. Злоумышленник в поле ввода комментария вводит вредоносный скрипт, который сохраняется на сервере. Теперь, пользователи, которые откроют страницу с комментариями к блогу, то получат вредоносный скрипт, загруженный злоумышленником.

### Пример 3
Пример с DOM XSS. В этом примере веб-приложение читает значение из поля ввода и записывает это значение в DOM.
```
var search = document.getElementById('search').value; 
var results = document.getElementById('results'); 
results.innerHTML = 'Search: ' + search;
```
Если злоумышленник может контроллировать поле ввода, то он может внедрить вредоностный скрипт.
```
Search: <img src=1 onerror=alert(1)>
```



## Что такое XSS?

XSS (Cross-site scripting) - уязвимость, которая возникает, при внедрении вредоносного сценария на веб-страницы, просматриваемые другими пользователями. 

Используется для кражи информации, может привести к захвату учетной записи, фишинговой атаке. Для защиты от атаки рекомендуется: 
- Проверять ввод
- Корректно кодировать выходные данные
- Использовать CSP
- Использовать HTTP заголовки безопасности

Это уязвимость, которая позволяет получить доступ к данным пользователей на уязвимом веб-сайте и выполнять действия от их лица с помощью выполнения js уязвимым веб-сайтом. XSS позволяет Выдавать себя за жертву, Выполнять действия, которые доступны жертве, Читать данные, которые доступны жертве, Внедрять троян в веб-приложение, Вносить изменения в веб-приложение.

## Stored XSS

Stored XSS - уязвимость, при которой скрипт поступает из БД. Уязвимость возникает, когда приложение получает данные из непроверенного источника, сохраняет их и в последующих ответах отображает их небезопасным способом.

Такие уязвимости возникают, когда злоумышленнику удается получить доступ к серверу и сохранить на нем вредоносный код. Такой сохраненный скрипт будет активироваться каждый раз, когда пользователь заходит на зараженную страницу. Часто хранимые XSS-уязвимости можно найти на различных форумах, а также в соцсетях, имиджбордах, блогах и других представителях Веб 2.0. Для их внедрения на ресурс злоумышленники могут использовать обычный текст (например, в виде комментариев), рисунки, гифки и другой размещаемый контент.

## Reflected XSS

Reflected XSS - уязвимость, при которой скрипт поступает из текущего запроса. Уязвимость возникает, когда приложение получает данные в запросе и немедленно возвращает их небезопасным способом.

Это наиболее распространенный сегодня тип XSS-атак. Их смысл в том, что злоумышленник прячет вредоносный код в заранее подготовленной ссылке, которую потом передает пользователям-жертвам в почтовой рассылке или размещает на веб-странице. Когда пользователь переходит по зараженной ссылке, серверные скрипты исполняют ее код без обработки и возвращают его в виде ответа. Браузер пользователя исполняет зараженный скрипт, а злоумышленник получает cookies жертвы.

## DOM-based XSS

DOM-based XSS - уязвимость в коде на стороне клиента, а не сервера. Возникает когда приложение содержит клиентский js, который обрабатывает данные из ненадежного источника небезопасным способом, обычно записывая обратно в DOM.

Суть этой уязвимости - в использовании Document Object Model, объектной модели документа. Это программный интерфейс, дающий программам и сценариям доступ к контенту HTML-, XHTML- и XML-документов и возможность этим содержанием (а также структурой и оформлением) управлять. XSS-уязвимости на основе DOM-модели могут быть как отраженными, так и хранимыми. Их главная особенность - сама страница сайта или веб-приложения не меняется, но меняется ее отображение в браузере пользователя из-за вредоносных модификаций DOM.

## XSS по степени активности

Также XSS-уязвимости различаются по степени активности:

Активная XSS - В этом случае уязвимости действуют сами независимо от активности пользователя. То есть, как только пользователь-жертва попадает на зараженную страницу, вредоносный скрипт запускается автоматически. Например, такие уязвимости встраиваются в базы данных или исполняемые файлы на сервере, в формы ввода (с помощью автоматических обработчиков событий) и т.д.

Пассивная XSS - В этом случае для активации вредоносного кода пользователь-жертва должен совершить какое-то действие: ввести запрос, кликнуть ссылку. Чтобы это произошло, злоумышленник должен как-то его заставить, спровоцировать или замотивировать. Здесь в ход идут методы социальной инженерии - например, фишинг.

## Последствия XSS-атаки

Сами XSS-уязвимости опасности для самого ресурса или его пользователей не представляют. Угрозу несут именно атаки, для которых эти бреши в обороне являются «точками входа». С помощью XSS-атаки можно нанести ресурсу и его пользователям разнообразный вред.

**Кража пользовательских данных.** Сайты и приложения, на которых предусмотрена авторизация пользователей, отличают авторизованного посетителя от неавторизованного с помощью специального cookie-файла (сессионной cookie). Этот файл будет видеть только авторизованный посетитель, а еще — любой JavaScript-код, исполняемый его браузером (в том числе и помещенный на страницу злоумышленником). Прочитав сессионную cookie жертвы, вредоносный скрипт через AJAX-запрос передает данные авторизации на удаленный сервер, предварительно созданный злоумышленником. Там эта информация заносится в базу данных, и остается только вычленить ее из параметров URL. Это дает следующие возможности:

- похитить конфиденциальные данные пользователя: историю посещений, информацию по платежным картам, личную переписку и т.д.;
- использовать аккаунт пользователя-жертвы для рассылки спама, угроз и другого противоправного контента, публикации комментариев с противозаконным или компрометирующим содержанием;
- рассылать вредоносные программы другим пользователям, особенно тем, у кого пользователь-жертва пользовался доверием;
- получить бесплатный доступ к платному контенту: учебным курсам, мультимедийному контенту и т.д.

**Доступ к управлению сайтом.** Злоумышленник может завладеть не только данными авторизованных пользователей, но и правами администратора сайта/веб-приложения. В этом случае он получает доступ к панели управления ресурсом и может контролировать его содержание, структуру, оформление. Такие возможности позволяют злоумышленнику:

- получить авторизационные данные любого зарегистрированного на сайте пользователя;
- испортить функционал и/или оформление сайта с целью его дискредитации в глазах настоящих или потенциальных посетителей;
- разместить на ресурсе посторонний, противозаконный или компрометирующий контент, внести изменения в уже имеющееся содержание (пресс-релизы, статьи, описания товаров и т.д.);
- взломать корпоративную базу данных для получения конфиденциальной информации (секретных технологий, сведений о коммерческой деятельности и т.д.).

Опасность не ограничивается лишь потерей или кражей данных, репутационными издержками - последствия могут быть куда серьезнее. Например, если злоумышленник получает на сайте фармацевтической компании доступ к описанию лекарства, он может изменить информацию по дозировке сильнодействующего лекарства. Как результат - массовые интоксикации с тяжелыми последствиями вплоть до летальных исходов.
