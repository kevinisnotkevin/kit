## Настройка и конфигурация
```bash
# Инициализация нового репозитория
git init

# Клонирование и создание копии удаленного репозитория
git clone <url>

# Глобальная конфигурация настроек Git. Эти параметры используются для идентификации автора коммитов
git config --global <setting_name> "value"

# Конфигурация настроек Git для конкретного репозитория. Переопределяет глобальные настройки для конкретного репозитория
git config --local <setting_name> "value"
```
## Операции с файлами
```bash
# Статус текущей ветки
git status

# Добавление файлов в буфер для коммита
git add <files>

# Удаление файла из буфера и репозитория
git rm <files>
# Удаление файла из буфера с сохранением в репозитории
git rm --cached <file_name>

# Переместить или переименовать файл
git mv <old_file> <new_file>

# Создание коммита с комментарием
git commit -m "Commit message"

# Разница между текущей директорией и последним коммитом
git diff
# Разница между двумя коммитами
git diff <commid_id1> <commit_id2>
```
## Branching and merging
```bash
# Список всех веток
git branch
# Создание новой ветки
git branch <branch_name>
# Удалить указанную ветку
git branch -d <branch_name>
# Список всех удаленных веток
git branch -r
# Удаленные ветки, которые могут быть merged с текущей веткой
git branch -r --merged
# Удаленные ветки, которые не могут быть merged с текущей веткой
git branch -r --no-merged

# Перемещение на другую ветку
git checkout <branch_name>
# Создание новой ветки на основании удаленной ветки
git checkout -b <branch_name> <remote_name>/<remote_branch>

# Слияние ветки в текущую ветку
git merge <branch_name>
# Отмена merge при наличии конфликтов
git merge --abort
```
## Удаленные репозитории
```bash
# Список удаленных репозиториев
git remote
# Создать удаленный репозиторий
git remote add <name> <url>
# Удалить удаленный репозиторий
git remote rm <remote_name>
# Информация об удаленном репозитории
git remote show <remote_name>
# Ветки удаленных репозиториев
git remote show <remote_name> --verbose
# Обновление локальных ссылок на удаленные ветки и теги
git remote update
# Переименование удаленного репозитория
git remote rename <old_name> <new_name>
# Изменение сслыки удаленного репозитория
git remote set-url <name> <new_url>
# Удаление ссылок на удаленные элементы, которых уже нет в удаленном репозитории
git remote prune <remote_name>

# Получение данных из удаленного репозитория
git fetch <remote_name>
# Синхронизация локального репозитория с удаленным. Обновляет все удаленные ветки, удаляет удаленные ветки
git fetch -p

# Получение данных из удаленного репозитория
git pull <remote_name> <remote_branch>

# Отправка данных на удаленный репозиторий
git push <remote_name> <remote_branch>
# Принудительная загрузка изменений на удаленный репозиторий
git push --force <remote_name> <local_branch>
```
## Commits
```bash
# История коммитов
git log
# История коммитов в кратком формате
git log --oneline
# История коммитов в виде графа. Отображает связи между ветками и их объединения
git log --graph
# История коммитов по автору
git log --author=<author_name>
# История коммитов с указанной даты
git log --since=<date>
# История коммитов до даты
git log --until=<date>
```
## Tags
```bash
# Список тегов
git tag

# Новый тег для коммита
git tag <tag_name> <commit_id>

# Создание тега с комментарием
git tag -a <tag_name> -m 'tag message'

# Удаление тега
git tag -d <tag_name>

# Удаление удаленного тега
git push <remote_name> --delete <tag_name>

# Информация о теге
git show <tag_name>
```
## Commit Management
```bash
# Изменение последнего коммита
git commit --amend

# Создание нового коммита, который отменяет действия предыдущего коммита
git revert <commit_id>

# Отменение изменений и перемещение HEAD на конкретный коммит
git reset --hard <commit_id>

# Переместить HEAD на конкретный коммит, но сохранить изменения
git reset --soft <commit_id>

# Запись всех изменений, внесенных в заголовок локального репозитория
git reflog
```
