Конфиг Unit  
```  
[Unit]  
Description=Sword of the Inquisition.  
Wants=SwordInquisition.timer  
  
[Service]  
Type=oneshot  
ExecStart=/usr/bin/df  
  
[Install]  
WantedBy=[multi-user.target](http://multi-user.target/)  
```
  
Конфиг таймера  
```
[Unit]  
Description=Sword of the Inquisition.  
Requires=SwordInquisition.service  
  
[Timer]  
Unit=SwordInquisition.service  
OnCalendar=*-*-* 09:50:00  
  
[Install]  
WantedBy=[timers.target](http://timers.target/)  
```

Примеры для OnCalendar  
```
*:*:00 - каждую минуту  
*:15:00 - в 15 минут каждого часа  
*-*-1,5,7 *:00:00 каждый час 1, 5 и 7 числа  
Mon *-*~1 если последний день месяца понедельник  
Mon *-*~7/1 последний понедельник месяца  
```
  
Алиасы
```
minutely - ежеминутно  
hourly - каждый час  
daily - каждый день  
monthly - каждый месяц  
weekly - еженедельно  
yearly - ежегодно  
quarterly - ежеквартально  
semiannually - раз в полгода  
```
  
Попробуй с помощью  
systemd-analyze calendar --iterations=5  
```
*-*-* 00:15:30  
Mon *-*-* 00:00:00  
Wed 2020-*-*  
Mon..Fri 2021-*-*  
2022-6,7,8-1,15 01:15:00  
Mon *-05~03  
Mon..Fri *-08~04  
*-05~03/2  
*-05-03/2
```  

#systemd/timer
