# WEB-SERVER

Часто WEB Application Server используют в связке с WEB Server, для увеличения производительности и пропускной способности Web Application Server, иначе увеличится время ожидания, нагрузка. WEB Server, например Nginx, работает быстрее, чем большинство WEB Application Server, поэтому у него в задачах отдавать статический контент, а когда нужно вызвать динамический контент, он работает в качестве прокси сервера, передает запрос WAS и обратно ответ (уже обработанный контент).

Динамический контент - контент, генерируемый на сервере в момент получения запроса (либо изменяемый пользователем, либо загружаемый из базы данных).

Статический контент - контент, хранимый на сервере в неизменяемом виде (например, бинарные файлы, аудио- и видеофайлы, JS и CSS).

## WEB server

Web server - статический веб-сервер.

- Отдавать статический контент.    
- Понимать протокол HTTP.
- Защищать информацию (tls).
- Идентифицировать пользователей / Вести логи.
- Передавать запрос в случае необходимости Web Application Server

## WEB application server

Сервер веб-приложений, для выдачи динамического контента. Задачи, отличные от статического веб-сервера:

- Реализует доступ к СУБД
- Реализует возможность исполнять код на ЯП (бизнес-логика)