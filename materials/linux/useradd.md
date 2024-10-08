# USERADD

```bash
# Создать пользователя user1, назначить домашний каталог /home/user1, назначить shell /bin/bash, включить в группу admin и добавить комментарий Comment
useradd -c "Comment" -g admin -d /home/user1 -s /bin/bash user1

# Создать пользователя user1
useradd user1

# Создать пользователя с домашним каталогом
useradd -m user1

# Создать пользователя и добавить в группу sudo
useradd -G sudo user1
```
