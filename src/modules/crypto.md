# Криптография
1. [hashlib - Безопасные хэши и дайджесты сообщений](#1)
2. [hmac - Хеширование ключей для аутентификации сообщений](#2)
3. [secrets - Генерация безопасных случайных чисел для управления секретами](#3)


## <div id="1">1. hashlib - Безопасные хэши и дайджесты сообщений</div>
> Модуль предоставляет функции для работы с алгоритмами хеширования (MD5, SHA1, SHA224, SHA256, SHA384, SHA512, серия SHA-3). Алгоритмы MD5, SHA1 имеют недостатки коллизий хешей.
### Использование
- Создание хеш-сумм файлов.
- Проверки целостности данных.
- Хранение паролей в безопасной форме.
- Применяется в сфере безопасности, цифровой подписи.
### Примеры
Создание хеш-сумм файла:
```python
def calculate_file_hash(file_path, hash_algorithm="sha256"):
    hash_object = hashlib.new(hash_algorithm)
    with open(file_path, "rb") as file:
        while chunk := file.read(8192):
            hash_object.update(chunk)
    return hash_object.hexdigest()

file_path = "example.txt"
file_hash = calculate_file_hash(file_path)
print(f"Hash of {file_path}: {file_hash}")
```
Хеширование пароля:
```python
def hash_password(password, salt):
    password_with_salt = password.encode('utf-8') + salt.encode('utf-8')
    hashed_password = hashlib.sha256(password_with_salt).hexdigest()
    return hashed_password

user_password = "secret_password"
salt = "random_salt"
hashed_password = hash_password(user_password, salt)
print(f"Hashed password: {hashed_password}")
```
Проверка целостности данных:
```python
def verify_integrity(original_data, received_hash, hash_algorithm="sha256"):
    hash_object = hashlib.new(hash_algorithm)
    hash_object.update(original_data)
    calculated_hash = hash_object.hexdigest()
    return received_hash == calculated_hash

data = b"Hello, world!"
hash_from_sender = "received_hash_value"
if verify_integrity(data, hash_from_sender):
    print("Data integrity verified.")
else:
    print("Data integrity compromised.")
```


## <div id="2">2. hmac - Хеширование ключей для аутентификации сообщений</div>
> Модуль реализует алгоритм HMAC, который является методом аутентификации сообщений с использованием хеш-функций. HMAC предоставляет способ обеспечения целостности и аутентичности данных, передаваемых между двумя сторонами. HMAC строится на основе хэш-функции и ключа, который известен обеим сторонам. Он используется для создания "подписи" сообщения, которая может быть проверена получателем для убеждения в том, что сообщение не было изменено и что оно происходит от ожидаемого отправителя.
### Использование
- Обеспечение целостности данных.
### Примеры
Используем хеш-функцию SHA-256 и секретный ключ для создания подписи. Полученная подпись может быть отправлена вместе с сообщением. Получатель использует тот же секретный ключ и хеш-фукнцию для проверки подписи.
```python
# Ваш секретный ключ
secret_key = b'my_secret_key'

# Сообщение, которое нужно подписать
message = b'Hello, world!'

# Создание подписи
signature = hmac.new(secret_key, message, digestmod='sha256').digest()

# Печать подписи
print(signature)
```


## <div id="3">3. secrets - Генерация безопасных случайных чисел для управления секретами</div>
> Модуль предоставляет функции для генерации криптостойких случайных чисел, подходящих для управления такими данными, как пароли, аутентификация учетных записей, токены безопасности и связанные с ними секреты.
### Использование
- Обеспечение безопасности при работе с паролями, сеансами аутентификации, шифрованием.
- Генерация псевдослучайных чисел.
- Используется в веб-приложениях.
### Примеры
```python
secure_password = secrets.token_hex(16)  # Генерация 16-байтного случайного пароля в виде шестнадцатеричной строки
print(secure_password)
```
