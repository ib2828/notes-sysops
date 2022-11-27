**systemd** — демон инициализации других демонов в Linux, пришедший на замену используемого ранее стартового демона /sbin/init. Его особенностью является интенсивное распараллеливание запуска служб в процессе загрузки системы, что позволяло существенно ускорить запуск операционной системы.

Units

В systemd есть понятие units:

- **service** — запускает, останавливает или перезагружает демоны, также можно запускать SysV-сценарии.
- **socket** — конфигурационный файл сокета, который связанный с определенным сервисом (service)
- **device** — конфигурационный файл содержащий правило udev для обработки дерева устройств.
- **mount** — монтирования файловой системы. Также можно получить информацию о файловой системы из файла /etc/fstab.
- **automount** — автоматическое монтирование файловой системы.
- **target** — логическая группировка единиц, ссылается на другие единицы. Например, bluetooth.target — запускает службы, при активации Bluetooth-устройства.
- **snapshot** — создание ссылок на другие единицы, восстанавливает список ранее запущенных служб.
- **timer** — подобие сron, активация единиц по таймеру.
- **swap** — управление файлами подкачки.
- **Rpath** — активация других служб на основе inotify

### Команды systemd:

**Список запущенных units:**

```systemctl```

**Запуск служб которые завершились неудачей:**

```systemctl --failed```

**Список доступных юнитов:**

```systemctl list-unit-files```

**Запуск/Остановка/Перезагрузка/Перезагрузка настроек unit:**

```systemctl start/stop/restart/reload <unit-name>```

**Просмотреть статус:**

```systemctl status <unit-name>```

**Проверить разрешен ли запуск юнита при старте системы:**

```systemctl is-enabled <unit-name>```

**Разрешить/запретить запуск юнита при старте системы:**

```systemctl enable/disable <unit-name>```

**Перезагрузка systemd с поиском измененных или новых юнитов:  

```systemctl daemon-reload```

**Все эти юниты разложены в трех каталогах:**

**/usr/lib/systemd/system/** – юниты из установленных пакетов RPM — всякие nginx, apache, mysql и прочее  
**/run/systemd/system/** — юниты, созданные в рантайме  
**/etc/systemd/system/** — юниты, созданные системным администратором

Юнит представляет из себя текстовый файл:

```
[Название секции в квадратных скобках]
имя_переменной = значение
```

> Для создания простейшего юнита надо описать три секции: [Unit], [Service], [Install] В секции Unit описываем, что это за юнит:

**Описание юнита:**

```Description=MyUnit```

**Запускать юнит после какого-либо сервиса или группы сервисов:**

```
After=syslog.target After=network.target After=nginx.service After=mysql.service
After=syslog.target
After=network.target
After=nginx.service
After=mysql.service
```

**Для запуска сервиса необходим запущенный сервис mysql:**

```Requires=mysql.service```

**Для запуска сервиса желателен запущенный сервис redis:**

```Wants=redis.service```


> Если сервис есть в Requires, но нет в After, то наш сервис будет запущен параллельно с требуемым сервисом, а не после успешной загрузки требуемого сервиса
> 
> В секции Service указываем какими командами и под каким пользователем надо запускать сервис

**Тип сервиса:**

```Type=simple```

> (по умолчанию): systemd предполагает, что служба будет запущена незамедлительно. Процесс при этом не должен разветвляться. Не используйте этот тип, если другие службы зависят от очередности при запуске данной службы.

```Type=forking```

> systemd предполагает, что служба запускается однократно и процесс разветвляется с завершением родительского процесса. Данный тип используется для запуска классических демонов.

**Также следует определить PIDFile=, чтобы systemd могла отслеживать основной процесс:**

```PIDFile=/work/www/myunit/shared/tmp/pids/service.pid```

**Рабочий каталог, он делается текущим перед запуском стартап команд:**

```WorkingDirectory=/work/www/myunit/current```

**Пользователь и группа, под которым надо стартовать сервис:**

```User=myunit Group=myunit```

**Переменные окружения:**

```Environment=RACK_ENV=production```

**Запрет на убийство сервиса вследствие нехватки памяти и срабатывания механизма OOM:**  
**-1000 полный запрет (такой у sshd стоит), -100 понизим вероятность.**

```OOMScoreAdjust=-100```


**Команды на старт/стоп и релоад сервиса:**

```
ExecStart=/usr/local/bin/bundle exec service -C 
 work/www/myunit/shared/config/service.rb --daemon

ExecStop=/usr/local/bin/bundle exec service -S /work/www/myunit/shared/tmp/pids/service.state stop 

ExecReload=/usr/local/bin/bundle exec service -S /work/www/myunit/shared/tmp/pids/service.state restart
```

**Таймаут в секундах, сколько ждать system отработки старт/стоп команд:**

```TimeoutSec=300```

**Попросим systemd автоматически рестартовать наш сервис, если он вдруг перестанет работать.**  
**Контроль ведется по наличию процесса из PID файла**

```Restart=always```

> В секции [Install] опишем, в каком уровне запуска должен стартовать сервис

**Уровень запуска:**
```WantedBy=multi-user.target```

> multi-user.target или runlevel3.target соответствует нашему привычному runlevel=3 «Многопользовательский режим без графики. Пользователи, как правило, входят в систему при помощи множества консолей или через сеть»

```
[Unit]
Description=MyUnit
After=syslog.target
After=network.target
After=nginx.service
After=mysql.service
Requires=mysql.service
Wants=redis.service

[Service]
Type=forking
PIDFile=/work/www/myunit/shared/tmp/pids/service.pid
WorkingDirectory=/work/www/myunit/current
User=myunit
Group=myunit
Environment=RACK_ENV=production
OOMScoreAdjust=-1000
ExecStart=/usr/local/bin/bundle exec service -C /work/www/myunit/shared/config/service.rb --daemon
ExecStop=/usr/local/bin/bundle exec service -S /work/www/myunit/shared/tmp/pids/service.state stop
ExecReload=/usr/local/bin/bundle exec service -S /work/www/myunit/shared/tmp/pids/service.state restart
TimeoutSec=300

[Install]
WantedBy=multi-user.target
```

Кладем этот файл в каталог /etc/systemd/system/  
Смотрим его статус
```systemctl status myunit```
```
myunit.service - MyUnit Loaded: loaded (/etc/systemd/system/myunit.service; disabled) Active: inactive (dead)

myunit.service - MyUnit

   Loaded: loaded (/etc/systemd/system/myunit.service; disabled)

   Active: inactive (dead)
```

Видим, что он disabled — разрешаем его  
```
systemctl enable myunit  
systemctl -l status myunit
```

Если нет никаких ошибок в юните — то вывод будет вот такой:
```
myunit.service - MyUnit Loaded: loaded (/etc/systemd/system/myunit.service; enabled) Active: inactive (dead)

myunit.service - MyUnit

   Loaded: loaded (/etc/systemd/system/myunit.service; enabled)

   Active: inactive (dead)
```

Запускаем сервис  
```systemctl start myunit```

Смотрим красивый статус:  
```systemctl -l status myunit```
** Если есть ошибки — читаем вывод в статусе, исправляем, не забываем после исправлений в юните перегружать демон systemd

```systemctl daemon-reload``` 

### Журнал:

Для регулирования размера файла логов, нужно отредактировать **/etc/systemd/journald.conf**  

```SystemMaxUse=100M```

**Чтение всех логов:**  

```journalctl```

**Логи с момента запуска системы:**  

```journalctl -b```

**Если был крах системы, можно ввести параметр -1 и посмотреть логи с предыдущего запуска системы (-2 с двух предыдущих и.т.д):**  

```journalctl -b -1```

**Все сообщение конкретной утилиты, например systemd:**  

```journalctl /usr/lib/systemd/systemd```

**Все сообщения конкретного процесса:**  

```journalctl _PID=1```

**Все сообщения конкретного юнита:**  

```journalctl -u netcfg```



#systemd