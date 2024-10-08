# CHATTR

**chattr (Change Attribute)** - это утилита командной строки в linux, которая используется для установки/снятия специальных атрибутов файлов для предотвращения случайной модификации и удаления файлов или директорий, даже если вы авторизованы как root.

Предположим вы хотите защитить некоторые важные файлы в Linux. При чем они должны быть защищены не только от перезаписи но и от случайного или преднамеренного удаления и перемещения. Предотвратить перезапись или изменение битов доступа к файлов можно с помощью стандартных утилит chmod и chown, но это не идеальное решение, так как у суперпользователя по прежнему остается полный доступ. Но есть еще одно решение. Это команда chattr. Эта утилита позволяет устанавливать и отключать атрибуты файлов, на уровне файловой системы не зависимо от стандартных (чтение, запись, выполнение). Для просмотра текущих атрибутов можно использовать lsattr. Изначально атрибуты управляемые chattr и lsattr поддерживались только файловыми системами семейства ext (ext2,ext3,ext4). но теперь эта возможность доступна и в других популярных файловых системах таких как XFS, Btrfs, ReiserFS, и т д. Утилиты `chattr` и `lsattr` входят в пакет e2fsprogs и предустановлены во всех современных дистрибутивах.

```bash
chattr опции [оператор][атрибуты] файлы
```
- Базовый синтаксис chattr выглядит следующим образом
```
-R - рекурсивная обработка каталога;
-V - максимально подробный вывод;
-f - игнорировать сообщения об ошибках;
-v - вывести версию.
```
- Основные опции
```
+ - включить выбранные атрибуты;
- - отключить выбранные атрибуты;
= - оставить значение атрибута таким, каким оно было у файла.
```
- Основные операторы
```
a - файл может быть открыт только в режиме добавления;
A - не обновлять время перезаписи;
c - автоматически сжимать при записи на диск;
C - отключить копирование при записи;
D - работает только для папки, когда установлен, все изменения синхронно записываются на диск сразу же;
e - использовать extent'ы блоков для хранения файла;
i - сделать неизменяемым;
j - все данные перед записью в файл будут записаны в журнал;
s - безопасное удаление с последующей перезаписью нулями;
S - синхронное обновление, изменения файлов с этим атрибутом будут сразу же записаны на диск;
t - файлы с этим атрибутом не будут хранится в отдельных блоках;
u - содержимое файлов с этим атрибутом не будет удалено при удалении самого файла и потом может быть восстановлено.
А теперь давайте перейдем к примерам работы с утилитой chattr и сделаем файл неизменяемым.
```
- Основные атрибуты
```bash
# Просмотр текущих атрибутов файла
lsattr /etc/resolv.conf

# Сделать файл неизменяемым. Теперь файл неизменяемый, и ни один пользователь не сможет его изменить. И более того, даже суперпользователь не сможет модифицировать, перезаписать или удалить этот файл. Перед тем как что либо делать с этим файлом нужно отключить атрибут
chattr +i /etc/resolv.conf

# Сделать все файлы в директории неизменямыми
chattr -R +i /etc/

# Разрешать только добавлять информацию в файл, без возможности перезаписи
chattr +a /var/log/syslog
```