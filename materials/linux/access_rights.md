# МОДЕЛЬ ПРАВ ДОСТУПА

## Права доступа

Обычные пользователи - Они создаются системным администратором. Каждый пользователь имеет числовой идентификатор пользователя, называемый UID

Системные пользователи - Системные пользователи создаются самой операционной системой. Скорее всего они используются какими-либо приложениями

Root - Данный пользователь имеет UID = 0 Root может выполнять все операции на низком уровне, запись в критическую область памяти и прочее Работать с root правами не рекомендуется.

Разрешения назначаются пользователям и группам. Каждый пользователь может быть членом различных групп, и членство в этих группах дает пользователю особые дополнительные разрешения. 

Каждый файл и каталог принадлежит определенному пользователю и определенной группе.

```bash
ls -l /bin/bash
# -rwxr-xr-x 1 root root 1265648 Mar 29  2024 /bin/bash
```
- Вывод прав доступа файла.
- Исполняемый файл `/bin/bash` принадлежит пользователю `root` и группе `root`. Модель прав доступа позволяет задать три независимых уровня прав на каждый объект файловой системы - для владельца, для группы и для всех остальных пользователей.
- Первый знак (`-`) содержит информацию о типе файла, в данном случае это обычный файл.

```
d - директория
l - символическая ссылка
c - устройство символьного ввода-вывода
b - устройство блочного ввода-вывода
p - FIFO
s - сокет
```
- Возможные типы файлов.

При создании новых файлов или каталогов, они принадлежат группе, к которой принадлежит пользователь, и пользователю.

Для перемещения по каталогу необходимы разрешения на выполнение, независимо от уровня доступа пользователя. Разрешения на выполнение каталога не позволяют пользователю выполнять или изменять какие-либо файлы или содержимое в каталоге, а только просматривать и получать доступ к содержимому каталога. Чтобы изменить содержимое каталога (создать, удалить или переименовать файлы и подкаталоги), пользователю необходимы права на запись в каталоге.

Вся система разрешений основана на восьмеричной системе счисления, и файлу или каталогу можно назначить три различных типа разрешений (`r` - Read, `w` - Write, `x` - Execute). `chmod` позволяет изменить разрешения (`u` - владелец, `g` - группа, `o` - другие, `a` - все пользователи), `+` или `-` для добавления и удаления назначенных разрешений.

```
rwx r-x r-x
```
- Права на файл из примера выше.
- Три блока соответсвуют трем типам пользователей.
- Первый блок - права владельца файла, второй - группы файла, третий - все остальные. 
- В данном случае владелец файла имеет право на чтение, запись, исполнение. Оставшиеся два блока отличаются тем, что нет прав на запись.

```bash
chown root:wheel /etc/passwd
```
- Изменение владельца и группы одновременно.

```bash
chown -R user /home/user
```
- Флаг `-R` позволяет рекурсивно изменять владельца или группу у всех обьектов в указанной директории.

## SUID & SGID

**SUID (Set User Identifier)** - это бит, который дает возможность программе выполняться от имени владельца файла, а не от имени текущего пользователя.

**SGID (Set Group Identifier)** - это бит, который дает возможность программе выполняться от имени группы файла, а не от группы текущего пользователя.

Если установить бит SGID на директорию, то все файлы, созданные в этой директории, будут автоматически наследовать группу этой директории, а не группу пользователя, который создаёт файлы. Это полезно для организации совместной работы, где файлы должны быть доступны членам определённой группы (например, в директориях для совместной разработки).

SUID, установленный на файл перекрывает SGID, установленный на каталог.

Вместо `x` используется `s`.

Когда речь заходит о директориях, появляются некоторые отличия. Директории используют те же флаги прав доступа, но их интерпретация имеет немного другой смысл.

- Флаг чтения разрешает просматривать список содержимого директории. Без флага чтения объекты файловой системы внутри директории нельзя просмотреть, но к объектам внутри директории все еще можно обратиться, если вы знаете полный путь к объекту на диске.
- Флаг записи разрешает создавать файлы в директории.
- Флаг исполнения разрешает войти в директорию и обращаться ко всем поддиректориям внутри. Без флага исполнения не будет доступа к объектам файловой системы внутри директории. 

```bash
mkdir /home/groupspace
chgrp mygroup /home/groupspace
chmod g+s /home/groupspace
```
- Создание дерева директорий, где все должно принадлежать одной группе.
- Любые пользователи группы `mygroup` могут создавать файлы и директории внутри `/home/groupspace` и им также будет автоматически задана принадлежность группе `mygroup`.

**ПРИМЕНЕНИЕ**

SUID позволяет запускать программы с повышенными привилегиями на ограниченный функционал, не предоставляя пользователям прямого доступа к важным ресурсам системы.

SGID позволяет членам группы запускать программы или редактировать файлы и директории с правами группы, даже если у них нет прямого доступа к этим ресурсам.

Есть достаточно много программ и файлов, которые должны принадлежать пользователю root, и в то же время - простые пользователи должны иметь возможность выполнять его.

Для примера - утилита passwd, которая находится в каталоге `/usr/bin/passwd` и которая имеет дело с файлом `/etc/passwd`, редактировать который может только пользователь root.

```bash
ls -l /usr/bin/passwd

# -rwsr-xr-x 1 root root ...
```
- Пример наличия SUID бита

```bash
chmod u+s /usr/bin/myapp
```
- Установка SUID бита.

```bash
chmod g-s /home/drobbins
```
- Удаление SGID бита с директории.

## Sticky Bit

Sticky Bit - это тип прав доступа к файлам, которые можно установить для каталогов. Этот тип разрешений обеспечивает дополнительный уровень безопасности при контроле удаления и переименования файлов в каталоге. Обычно он используется в каталогах, которые используются несколькими пользователями, чтобы предотвратить случайное удаление или переименование одним пользователем файлов, которые важны для других. 

При использовании пользователь сможет удалить файл, только если будет являться владельцем этого файла или владельцем катаога, в котором содержится файл.

Например, в общем домашнем каталоге, где несколько пользователей имеют доступ к одному и тому же каталогу, системный администратор может установить Sticky Bit для каталога, чтобы гарантировать, что только владелец файла, владелец каталога или пользователь root может удалять или переименовывать файлы в каталоге. Это означает, что другие пользователи не могут удалять или переименовывать файлы в каталоге, поскольку у них нет необходимых разрешений. Это обеспечивает дополнительный уровень безопасности для защиты важных файлов, поскольку только те, у кого есть необходимый доступ, могут удалять или переименовывать файлы. Установка Sticky Bit для каталога гарантирует, что только владелец, владелец каталога или пользователь root смогут изменять файлы в каталоге.

Когда для каталога установлен Sticky Bit, он обозначается буквой «t» в разрешении на выполнение разрешений каталога. Например, если каталог имеет разрешения «rwxrwxrwt», это означает, что установлен Sticky Bit, что дает дополнительный уровень безопасности, чтобы никто, кроме владельца или пользователя root, не мог удалять или переименовывать файлы или папки в каталоге.

Если Sticky Bit написан с заглавной буквы (T), то это означает, что все остальные пользователи не имеют разрешений на выполнение (x) и, следовательно, не могут видеть содержимое папки и запускать из нее какие-либо программы. Sticky Bit в нижнем регистре (t) - это Sticky Bit, в котором установлены разрешения на выполнение (x).

## UMASK

**UMASK (User file-creation mode Mask, Маска режима создания файлов Пользователя)** - это маска доступа, которая определяет, какие разрешения не будут установлены при создании новых файлов и каталогов.

Когда процесс создает новый файл, он указывает, какие права доступа нужно задать для данного файла. Зачастую запрашиваются права 666 (чтение и запись всеми), что дает больше разрешений, чем необходимо в большинстве случаев. 

Каждый раз, когда в Linux создается новый файл, система обращается к параметру, называемому umask. Система использует значение umask чтобы понизить изначально задаваемые разрешения на что-то более разумное и безопасное. В Linux-системах значением по умолчанию для umask является 022, что позволяет читать чужие новые файлы, но не изменять их.

```bash
umask

# 022
```
- Просмотр текущих настроек umask.
- Владелец будет иметь полный доступ, а группа и остальные пользователи будут иметь права на чтение и исполнение.

```bash
umask 077
```
- Изменение настройки umask.
- Владелец будет иметь полный доступ, а группа и остальные пользователи не будут иметь никаких прав доступа для созданных файлов.

Например, при создании пользователя с `umask 077` с домашней директорией, эта директория будет иметь права доступа `rwx------`, т.е. никто, кроме созданного пользователя не будет иметь доступ к домашней директории. Владельцем этой директории будет установлен новый пользователь, а группой будет установлена группа с тем же именем, что и пользователь.

Для создаваемых файлов права доступа будут `600`, а для создаваемых каталогов права доступа будут `700`. Так как стандартные права по умолчанию для файлов `666`, для директорий `777`.