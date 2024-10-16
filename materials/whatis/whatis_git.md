# Система контроля версий

Система контроля версий - это система, позволяющая записать действия выполненные над файлами. Действием может быть создание, удаление или изменение файла.

Такие системы позволяют документировать историю изменений, позволяют нескольким разработчикам работать над одним проектом одновременно, а также позволяют откатить версии приложения, если в проекте что-то сломалось.

Git - это одна из систем контроля версий. Она была создана сообществом разработчиков ядра Linux, включая Линуса Торвальдса - создателя Linux, в поисках замены для BitKeeper.

> Система контроля версий - это один из ключевых инструментов разработки, позволяющий наладить непрерывную интеграцию и развертывание ПО (CI/CD) в процессе работы над проектом

## 1 Цели, которые преследовались при создании Git

- Простота архитектуры
- Скорость работы
- Поддержка нелинейной разработки
- Эффективность в управлении большими проектами
- Децентрализация

## 2 Снимки в Git

В отличие от большинства других систем, которые хранят информацию в виде списка изменений в файлах, Git хранит данные в виде набора снимков миниатюрной файловой системы. Каждый раз, когда вы сохраняете состояние своего проекта в Git (git commit), система запоминает, как выглядит каждый файл в этот момент, и сохраняет ссылку на этот снимок. Если файлы не были изменены, Git не запоминает эти файлы вновь, а только создаёт ссылку на предыдущую версию идентичного файла, который уже сохранён.

## 3 Большинство операции выполняются локально

Практически все операции выполняются на локальной версии проекта. Не важно создали вы свой проект или клонировали с GitHub. Команды могут выполняться не локально, когда вы работаете с удаленными репозиториями, серверами Git или Git-хостингами, а также когда вы используете графические интерфейсы или веб-интерфейсы для работы с Git.

## 4 Целостность Git

При создании коммитов Git вычисляет хэш сумму этого коммита. Как только файл будет изменен, изменится его и хэш сумма, а значит Git увидит факт изменения. Так, Git сохраняет все файлы не по имени, а по хэшу этих файлов.

## 5 Четыре состояния

Последняя важная часть без которой работа с Git невозможна - это состояния.

Состояния, в которых могут находиться файлы:

- изменён (modified)
- индексирован (staged)
- зафиксирован (committed)
- не отслеживаемый (untracked)

![](/materials/images/devops/devops_git_stages.png)

Индексированные файлы - это файлы, которые присутствовали в последнем снимке. Эти файлы могут быть изменёнными или готовыми к следующему коммиту. Соответственно все остальные файлы, которые не индексированы, будут иметь состояние не отслеживаемые.

Как только выполнено редактирование файла, он получает статус изменен и в при просмотре статусов файл будет выделен красным. При выполнении снимка в этом состоянии git не сохранит изменение при коммите. Для того чтобы он это сделал нужно индексировать этот файл. Commited - это состояние файла при выполнении снимка, выдается сразу после коммита. Это значит, что при редактировании файла, мы сможем вернуть его содержимое на момент создания снимка. После редактирования файла статус будет modified и цикл снова повторяется.

## 6 Ветки

Как мы помним, git не хранит данные в виде последовательности изменений, а хранит в виде снимков. Когда делается коммит, git сохраняет его в виде объекта, который указывает на снимок подготовленных данных. Этот объект также содержит информацию об авторе и его электронной почте, сообщение, которое вы ввели при коммите, и указатель на предыдущий коммит или коммиты, которые непосредственно предшествуют данному коммиту. Если это первый коммит, то у него не будет родителя. Если это обычный коммит, то у него будет один родитель, а если это результат слияния двух и более веток, то у него будет несколько родителей.

Помимо этого вычисляется контрольная сумма (хэш), что позволяет отслеживать как факт изменения коммита, так и дает своего рода уникальный идентификатор.

Ветка в Git - это перемещаемый указатель на один из коммитов. Имя основной ветки - master. При создании новых коммитов, ветка (или указатель) master будет указывать на последний коммит. Обычно ветки создают для нового направления разработки. Например: добавление новой функции, фикс багов, разработка новой функции. Ветки в Git помогают работать над проектом более гибко и эффективно.

В конечном итоге цель создания каждой конкретной ветке достигается и с ней нужно что-то делать дальше. А именно её нужно слить/вернуть в основную ветку, то есть все изменение выполнить в обеих ветках. В этом случае слитая ветка становится неактивной и ее удаляют.

Иногда случается так, что слить ветки невозможно. Происходит это, когда в двух ветках изменена одна и та же часть файла. В этом случае необходимо решить конфликт, выбрав нужное изменение, и выполнить коммит.

## 7 GitHub и GitLab. Удаленный репозиторий

Удаленный репозиторий представляет собой сохраненный проект находящийся на удаленном сервере где-то в сети. Это позволяет нескольким разработчикам работать над одним и тем же проектом, синхронизируя изменения между их локальными репозиториями и удаленным репозиторием. По большей части GitHub и GitLab являются такими удаленными репозиториями.

## 8 Github flow

GitHub flow - это набор практик, облегчающий и оптимизирующий работу процесса с Git. Существуют и другие практики, но их мы не коснёмся.

Практики GitHub flow описывают несколько этапов:

1. Создается новая ветка, чтобы начать работу над новой функцией или исправлением ошибки.
2. После выполнения всех изменений разработчик коммитит их. Каждый коммит имеет объяснение. Понятное описание даст возможность понять другим разработчикам ваши намерения и насколько изменения им соответствуют.
3. Выполнить запрос на слияние(pull request). Запрос на слияние может быть выполнен в любом из случаев: - вы готовы к проверке вашей работы - вы столкнулись с проблемами и нуждаетесь в помощи - вы хотите поделиться некоторыми соображениями и/или скриншотами с командой.
4. После идет обсуждения запроса на слияния. Если есть какие-то доработки, то их можно отправить помощью команды push. GitHub покажет ваши новые фиксации и любую дополнительную информацию.
5. Теперь после проверки можно влить в основную. После слияния создаётся фиксация со всеми изменениями из вашей ветки.

В других подходах сначала происходит слияние, потом различные тесты. Если что-то не стыкуется, то для отката и фиксов потребуется время, что может привести к переносу релиза.

## 9 GitHub или GitLab

GitHub и GitLab являются веб-репозиториями Git, важными для успешной разработки программного обеспечения. Они помогают управлять изменениями в проектах с открытым исходным кодом и сохраняют эффективность разработки. GitHub является самой популярной платформой для управления репозиториями на основе git, с более 28 миллионами пользователей и 85 миллионами репозиториев кода. GitHub поддерживает размещение открытого исходного кода, но не является полностью открытым. GitHub делает упор на высокую доступность и производительность инфраструктуры.

GitLab также является веб-репозиторием git. Он набирает популярность среди разработчиков проектов с открытым исходным кодом. GitLab является полноценной DevOps системой на хорошо интегрированной платформе. Важным плюсом GitLab можно считать возможность установить его локально.

## 10 GitLab Runner

GitLab Runner - агент который занимается выполнением инструкций .gitlab-ci.yaml. В отличие от Jenkins, он написан на GO, поэтому он быстрый и мало весит. Так же Runner умеет запускать задачи разными способами: локально, в докер-контейнерах, в различных облаках или через ssh-коннект к какому либо серверу.

- [https://git-scm.com/book/ru/v2/](https://git-scm.com/book/ru/v2/)
