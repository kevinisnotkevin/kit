# Проверка пароля на компрометацию

Вводить свой пароль на веб-сайты сторонних сервисов, включая **`haveibeenpwned.com`**, является плохой практикой по следующим причинам:
1. **Безопасность пароля**: При вводе пароля на веб-сайт, вы предоставляете доступ к нему этому веб-сайту. Даже если веб-сайт утверждает, что использует пароль только для проверки утечек данных, нет гарантии, что пароль не будет сохранен или использован в дальнейшем
2. **Риски утечки данных**: Есть шанс, что сайт может быть скомпрометирован или взломан, что приведет к утечке пароля
3. Не рекомендуется вводить свои пароли в открытые API-запросы, в том числе и через **`curl`**, чтобы избежать передачи чувствительной информации через незащищенные каналы
4. **Альтернативные методы проверки**: Существуют альтернативные методы проверки паролей на утечку, которые не требуют ввода самого пароля, например, использование хэшированных версий паролей или API-интерфейсов

# Пример

```bash
 curl <https://api.pwnedpasswords.com/range/21BD1>
```
- Данный запрос осуществляет обращение к сервису **"Have I Been Pwned"** для получения хэшей утечек паролей, начинающихся с заданного префикса. Этот сервис предоставляет информацию о том, сколько раз в утечках были обнаружены пароли, начинающиеся с заданного префикса
```jsx
008A8D00FC1A05382C1B99D1D9F1C6526E1D:1
00EB2B000E8B5C32F6475E5F65C6E2F28F7A:1
01172BEE22E75029BFB5D85A9C42F73C5E2C:3
```
- Пример ответа на запрос. Каждая строка представляет собой хэш пароля и количество раз, которое этот хэш был обнаружен в утечках

## 1.1 Теперь разберёмся с хэшами паролей

Хеш пароля в Linux - это хэшированное представление пароля пользователя, которое используется для хранения паролей в зашифрованном виде в системных файлах, таких как **`/etc/shadow`**. Вместо хранения пароля в открытом тексте, Linux сохраняет его хэш, который является результатом применения к паролю криптографической хеш-функции.

Когда пользователь вводит свой пароль, Linux вычисляет хеш этого введенного пароля и сравнивает его с хешем, хранящимся в файле **`/etc/shadow`**. Если хеши совпадают, то пароль считается правильным и пользователь получает доступ.

Это повышает безопасность, потому что даже администратор не может прочитать пароли в открытом виде. Кроме того, использование хешей делает невозможным восстановление оригинального пароля из хранимого значения, поскольку хеш-функции являются односторонними.

**Так выглядит строка пренадлежащая пользователю igor в `/etc/shadow`**

```jsx
igor:$6$Ep.RO7BYVboqYJK.$6gX.itPm3tv0Zpb1me8ezAdUNKdESz3J0wSJFE5NsQ43W7a0sHMRcGIH4bYFHynp9LPqwXKH.xkqZt16ch1ki1:19637:0:99999:7:::
```

1. **igor:** Это имя пользователя.
2. **$6$Ep.RO7BYVboqYJK.$6gX.itPm3tv0Zpb1me8ezAdUNKdESz3J0wSJFE5NsQ43W7a0sHMRcGIH4bYFHynp9LPqwXKH.xkqZt16ch1ki1:** Этот длинный хэш пароля представляет собой результат хэширования фактического пароля пользователя "igor". Этот хэш создан с использованием алгоритма SHA-512 и включает в себя соль, чтобы обеспечить дополнительную защиту от атак по словарю. Поскольку хэш пароля является хэшированным представлением исходного пароля, его невозможно обратно преобразовать в исходный пароль.
3. Здесь используется алгоритм хэширования SHA-512. Он состоит из нескольких частей, разделенных символом **`$`**. Первая часть (**`$6$`**) указывает на использование алгоритма SHA-512. Далее идет соль, которая обеспечивает дополнительную защиту от атак по словарю, и непосредственно хэш пароля.
4. **19637:** Это количество дней, прошедших с 1 января 1970 года до последнего изменения пароля (Unix-время).
5. **0:** Это минимальное количество дней, которое должно пройти перед изменением пароля.
6. **99999:** Это максимальное количество дней, в течение которых пароль будет действителен, после чего пользователю потребуется изменить пароль.
7. **7:** Это количество дней, за которое пользователь получит предупреждение о том, что его пароль скоро истечет.
8. **:::** Эти поля представляют собой зарезервированные поля, которые не используются.

> **Соль (salt) в контексте хеширования паролей** - это случайная последовательность битов, которая добавляется к паролю перед его хешированием. Соль используется для того, чтобы усложнить процесс взлома пароля при помощи атак по словарю или радужных таблиц.

Каждый раз, когда создается новый пароль или изменяется существующий, генерируется новая соль. После этого соль добавляется к паролю, и затем вместе они хешируются с использованием криптографической хеш-функции. Этот хеш вместе с солью затем сохраняется в хранилище паролей.

Использование соли увеличивает безопасность паролей, потому что даже если два пользователя используют одинаковые пароли, их хеши будут различаться из-за разных солей. Это делает невозможным использование предварительно подготовленных радужных таблиц или атак по словарю, поскольку для каждого пароля необходимо создавать уникальную таблицу хешей с учетом соли.

## 1.2 Теперь зная, что такое хеш, мы можешь идти дальше и тут есть несколько нюансов, давайте разбираться

<aside> ⚠️ К сожалению, пароли, хранящиеся в системе Linux в файле **`/etc/shadow`**, обычно хешируются с использованием алгоритмов, таких как SHA-512, а не SHA-1. Процесс преобразования пароля, хешированного с использованием SHA-512, в SHA-1 не является обратимым и не может быть выполнен без доступа к исходному паролю.

</aside>

SHA-1 и SHA-512 - это разные алгоритмы хеширования, и не существует прямого способа преобразовать SHA-512 хеш обратно в SHA-1 без знания исходного пароля.

Тем не менее, если у вас есть доступ к исходному паролю "igor", вы можете легко сгенерировать его хеш с использованием алгоритма SHA-1 с помощью множества инструментов и онлайн-ресурсов.

## Скрипт проверки пароля на компрометацию

```bash
#!/bin/bash
candidate_password=$1
echo "Candidate password: $candidate_password"
full_hash=$(echo -n $candidate_password | sha1sum | awk '{print substr($1, 0, 32)}')
prefix=$(echo -n $full_hash | awk '{print substr($1, 0, 5)}')
suffix=$(echo -n $full_hash | awk '{print substr($1, 6, 26)}')
if curl <https://api.pwnedpasswords.com/range/$prefix> | grep -i $suffix
then echo "Пароль найден в базе утечек данных."
else echo "Пароль не найден в базе утечек данных."
fi
```
