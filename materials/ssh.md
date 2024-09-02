# SSH

- __SSH (Secure Shell)__ – криптографический протокол прикладного уровня, который обеспечивает безопасный и зашифрованный метод управления сетевыми устройствами и доступа к удаленным серверам. Раньше для удаленного доступа использовались протоколы Telnet, Rlogin, Rsh. Поддерживает несколько методов аутентификации (на основе пароля, открытого ключа, на основе хоста). Предоставляет два протокола передачи файлов: SCP, SFTP.
- Чтобы снизить риск несанкционированного доступа, рекомендуется отключить прямой вход в систему root, использовать стандартную учетную запись пользователя с привилегиями sudo, ограничить доступ по SSH определенным IP-адресам.
- Для повышения безопасности необходимо сменить стандартный номер порта, запретить входить напрямую пользователем root в конфигурациях.
- Чтобы подключиться к удаленному хосту Linux через SSH, соответствующий SSH-сервер должен быть доступен и запущен. Наиболее часто используемый SSH-сервер — это сервер OpenSSH. OpenSSH — это бесплатная реализация протокола Secure Shell (SSH) с открытым исходным кодом, которая обеспечивает безопасную передачу данных и команд по сети. OpenSSH можно настроить, отредактировав файл /etc/ssh/sshd_config. Можно настроить такие параметры, как максимальное количество одновременных подключений, использование паролей или ключей для входа в систему, проверку ключей хоста и многое другое.


## Принцип работы

- Открытие транспортного канала аутентификации (Трехстороннее рукопожатие);
- Отправляющий TCP отправляет сегмент SYN принимающему TCP;
- Принимающий TCP возвращает сегмент ACK для подтверждения получения SYN;
- Отправляющий TCP отправляет еще один ACK и переходит к отправке данных;
- Аутентификация;
- Происходит обмен данными о версиях протокола SSH, шифровании, методе обмена ключами, алгоритмами сжатия данных;
- Подключение.


### Отключение запроса пароля

Суть приватных ключей в том, что они хранятся на локальных компьютерах. Если вы попытаетесь подключиться к серверу с другой машины, на которой нет ключа, то снова увидите запрос на ввод пароля. Чтобы авторизоваться можно было только по ключу, запретите использование пароля.

1. Подключитесь к удаленному серверу.
2. Выполните команду **sudo nano /etc/ssh/sshd_config**. Файл **sshd_config** откроется во встроенном текстовом редакторе. 
3. Найдите строку **PasswordAuthentication yes** и измените ее на **PasswordAuthentication no**.
4. Сохраните изменения и перезапустите службу SSH командой **sudo service ssh restart**.

Авторизация по паролю отключена. Теперь подключиться к серверу можно только с помощью пары ключей.


## Usage

SSH config
```bash
/etc/ssh/sshd/config

# Changes default SSH port (22)
Port 9809

# Disables root login
PermitRootLogin no

# Restricts access to specificusers
AllowUsers user1, user2

# Enables login through ssh key
PubkeyAuthentication yes

# Disables login through password
PasswordAuthentication no

# Disables usage of files .rhosts and .shosts
IgnoreRhosts yes

# Disables a less secure type of login
HostbasedAuthentication no

# Number of unauthenticated connections before dropping
MaxStartups 10:30:100

# No. of failed tries before the servers stops accepting new tries
MaxAuthTries 3

# Max current ssh sessions
MaxSessions 1

# Disables interactive password authentication
ChallengeResponseAuthentication no

# No empty password allowed
PermitEmptyPasswords no

# Disables Rhost authentication
RhostsAuthentication no

# Disables port forwarding (blocks i.e MySQL Workbrench)
AllowTcpForwarding no
X11Forwarding no

# Prints much more info about SSH connections
LogLevel VERBOSE
```

SSH connections

```bash
# Connects to a server (default port 22)
ssh user@server

# Uses a specific port declared in sshd_config
ssh user@server -p other_port

# Runs a script on a remote server
ssh user@server script_to_run

# Invoke a local script
ssh user@server bash < script.sh

# Compresses and downloads from a remote server
ssh user@server "tar cvzf - ~/source" > output.tgz

# Specifies other ssh key for connection
ssh -i ~/.ssh/specific_ssh_key
```

SCP

```bash
# Copies a file from a remote server to a local machine
scp user@server:/dir/file.txt local_dest/

# Copies a file between two servers
scp user@server:/dir/file.txt user@server:/dir

# Copies a file from a local machine to a remote server
scp local_dest/file.txt user@server:/dir

# Uses a specific port declared for SSH in sshd_config
scp -P port

# Coppies recursive a whole folder
scp -r user@server:/dir local_dest/

# Coppies all files from a folder
scp -r user@server:/dir/* local_dest/

# Copies all files from a server folder to the current folder
scp user@server:/dir/* .

# Compresses data on network using gzip
scp -C

# Prints verbose info about the current transfer
scp -v
```

SSH keys

```bash
# Generates a new ssh key (-t – type of key, -b – the number of bits in the key)
ssh-keygen -t rsa -b 4096

# Sends the key to the server
ssh-copy-id user@example.com

# Converts ids_rsa into ppk
puttygen current_key -o keyname.ppk
```

.ssh

```bash
# User-specific config.
~/.ssh/config

# Private key.
~/.ssh/id_type

# Public key.
~/.ssh/id_type.pub

# Logged in host.
~/.ssh/known_hosts

# Authorized login key.
~/.ssh/authorized_keys
```
