# TAR

Команда **tar** является наиболее часто используемой командой для архивирования нескольких файлов в **tarball** — общий формат файлов Linux, аналогичный формату zip, при этом сжатие является опциональным.

Это довольно сложная команда с длинным списком функций, таких как добавление новых файлов в существующий архив, перечисление содержимого архива, извлечение содержимого из архива и многое другое.

```bash
# Создать сжатый архив из каталога folder
tar -czvf archive.tar.gz folder/

# Распаковать архив
tar -xvf archive.tar

# Показать содержимое архива без распаковки
tar -tf archive.tar
```