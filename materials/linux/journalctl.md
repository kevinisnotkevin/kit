# journalctl

journalctl - это утилита для работы с журналами событий ОС линукс.

```bash
journalctl -u sshd.service
journalctl /usr/bin/sshd
journalctl -u "sshd*"
journalctl | grep -i ssh
journalctl | grep -i 'Failed password for'
```
- Способы вывода логов ssh.
