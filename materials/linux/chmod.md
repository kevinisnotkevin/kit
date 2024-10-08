# CHMOD

```bash
# Аналогично chmod 777 dir1. Выдать права ugo(User Group Other)+rwx(Read Write eXecute) на директорию dir1
chmod ugo+rwx dir1

# Забрать права у группы и всех остальных все права для директории dir1
chmod go-rmw dir1

# Назначить SUID-бит файлу file1. Дает возможность любому пользователю запускать на выполнение файл с полномочиями владельца файла
chmod u+s file1

# Назначить SGID-бит для файла file1
chmod g+s file1

# Снять SGID-бит для файла file1
chmod g-s file1

# Назначить STIKY-бит для файла file1. Позволяет удалять только владельцам
chmod o+t file1

# Снять STIKY-бит для файла file1
chmod o-t file1

# Рекурсивная установка прав для всех каталогов и файлов в каталоге dir
chmod -R 755 dir
```
