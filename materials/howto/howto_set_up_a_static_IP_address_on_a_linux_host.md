# Как настроить статический IP-адрес

Чтобы настроить статический IP-адрес необходимо перейти в файл /etc/network/interfaces и прописать следющие настройки: 
1. auto [interface name] 
2. iface eth0 inet static 
3. address [ip address] 
4. netmask [mask] 
5. gateway [gateway ip] 
6. broadcast [broadcast address] 
7. dns-nameserver [dns-ip]

```bash
service networking restart
```
- Далее, чтобы применить настройки необходимо выполнить команду 
