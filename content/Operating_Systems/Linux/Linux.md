
#### ***Добавление dns серверов в систему*** <a name=systemd_add_dns_servers></a></br>
Для добавления своих DNS серверов необходимо прописать в файле /etc/systemd/resolved.conf
```
$ cat /etc/systemd/resolved.conf
<...>
[Resolve]
DNS=8.8.8.8 8.8.4.4
<...>
```
Необходимо перезагрузить systemd-resolved.service или перезагрузить компьютер.

#systemd/resolved 

---

#### ***Очистка dns кэша***
```sudo systemd-resolve --flush-caches```

#systemd/resolved


