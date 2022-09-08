[Копирование папки на удаленный сервер](#copy_dir_to_remote_server) </br>


#### ***Копирование папки на удаленный сервер*** <a name="copy_dir_to_remote_server"></a></br>
```tar -cv arcdir22 | ssh 192.168.0.1 sudo -Hu appuser tar -xC /home/appuser/arcdir22```</br>
```tar cBvf - arcdir22 | ssh 192.168.0.1 tar xBvpf -```
</br>

