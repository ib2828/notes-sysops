Если вы столкнулись с тем, что директория /var/log/journal — стала заниматься слишком много места и ее нужно очистить:

953M /var/log/journal

До очистки:
```
journalctl --disk-usage
```
Archived and active journals take up 952.2M on disk.

Делаем чистку:

```
journalctl --vacuum-size=50M
journalctl --verify
```


После очистки получаем результат:
```
journalctl --disk-usage
```
Archived and active journals take up 56.0M on disk.

Меняем конфиг:
```
vi /etc/systemd/journald.conf
```

```
SystemMaxUse=50M
SystemMaxFileSize=12M
```

```
systemctl restart systemd-journald
```

#linux/journalctl
#linux/log


## Фильтрация логов

### Логи с момента последней загрузки

С помощью опции `-b` можно просмотреть все логи, собранные с момента последней загрузки системы:

$ journalctl -b
-- Logs begin at Wed 2019-11-13 15:01:02 MSK, end at Sat 2020-03-14 09:12:15 MSK
мар 14 09:03:09 web-server kernel: Linux version 5.3.0-40-generic (buildd@lcy01-
мар 14 09:03:09 web-server kernel: Command line: BOOT_IMAGE=/boot/vmlinuz-5.3.0-
мар 14 09:03:09 web-server kernel: KERNEL supported cpus:
мар 14 09:03:09 web-server kernel:   Intel GenuineIntel
мар 14 09:03:09 web-server kernel:   AMD AuthenticAMD
мар 14 09:03:09 web-server kernel:   Hygon HygonGenuine
мар 14 09:03:09 web-server kernel:   Centaur CentaurHaulsКопировать

### Просмотр логов предыдущих сессий

Можно просматривать информацию о предыдущих сессиях работы в системе. Получаем список предыдущих загрузок:

$ journalctl --list-boots
-46 6ab45fd4f20b4c688367749c5a319c36 Wed 2019-11-13 15:01:02 MSK—Wed 2019-11-13 15:02:54 MSK
-45 483b31301b0f49e781cf8441aaf209af Wed 2019-11-13 15:04:38 MSK—Wed 2019-11-13 15:06:14 MSK
-44 ea2435c1f0aa4b6eb6ad5f0efd7bb779 Wed 2019-11-13 15:06:26 MSK—Wed 2019-11-13 15:24:00 MSK
..........
 -2 304d7cadb64a46e185fad345f732ec0b Mon 2020-03-02 10:55:49 MSK—Mon 2020-03-02 16:48:21 MSK
 -1 c0ed77121b1c4e53a01e78c3666a4389 Fri 2020-03-06 14:52:51 MSK—Fri 2020-03-06 18:18:19 MSK
  0 49f60c5b2dbe48a1955e892db356ec66 Sat 2020-03-14 09:03:09 MSK—Sat 2020-03-14 09:17:37 MSKКопировать

В первой колонке указывается порядковый номер загрузки, во второй — её идентификатор, в третьей — дата и время. Чтобы просмотреть лог для конкретной загрузки, можно использовать идентификаторы как из первой, так и из второй колонки:

$ journalctl -b -1Копировать

$ journalctl -b c0ed77121b1c4e53a01e78c3666a4389Копировать

Сохранение информации о предыдущих сессиях поддерживается по умолчанию не во всех дистрибутивах, иногда его требуется активировать.

$ sudo nano /etc/systemd/journald.confКопировать

[Journal]
Storage=persistentКопировать

### Фильтрация по дате и времени

Для этого используются опции `since` и `until`. Например, нам нужно просмотреть логи, начиная с 14 часов 55 минут 6 марта 2020 года:

$ journalctl --since "2020-03-06 14:55:00"Копировать

Можно воспользоваться и вот такими человекопонятными конструкциями:

$ journalctl ---since yesterday
$ journalctl --since 09:00 --until now
$ journalctl --since 10:00 --until "1 hour ago"Копировать

### Фильтрация по приложениям и службам

Для просмотра логов конкретного приложения или службы:

$ journalctl -u mysql.serviceКопировать

$ journalctl -u mysql.service --since yesterdayКопировать

### Фильтрация по процессам, пользователям и группам

Просмотреть логи для какого-либо процесса можно, указав его идентификатор:

$ journalctl _PID=655Копировать

$ pstree -p
systemd(1)─┬─ModemManager(542)─┬─{ModemManager}(572)
           │                   └─{ModemManager}(576)
           ├─NetworkManager(527)─┬─dhclient(715)
           │                     ├─{NetworkManager}(587)
           │                     └─{NetworkManager}(591)
           ├─accounts-daemon(533)─┬─{accounts-daemon}(539)
           │                      └─{accounts-daemon}(545)
           ├─acpid(494)
           ├─apache2(655)─┬─apache2(4385)─┬─{apache2}(4415)
           │              │               ├─{apache2}(4416)
           │              │               ├─{apache2}(4417)
           │              │               ├─{apache2}(4418)
           │              │               └─{apache2}(4440)
           │              └─apache2(4386)─┬─{apache2}(4389)
           │                              ├─{apache2}(4390)
           │                              ├─{apache2}(4391)
           │                              ├─{apache2}(4392)
           │                              └─{apache2}(4414)Копировать

Для просмотра логов процессов, запущенных от имени определённого пользователя или группы, используются фильтры `_UID` и `_GID` соответственно. Предположим, у нас есть веб-сервер, запущенный от имени пользователя `www-data`. Определим сначала идентификатор этого пользователя:

$ id -u www-data
33Копировать

Теперь можно просмотреть логи всех процессов, запущенных от имени этого пользователя:

$ journalctl _UID=33Копировать

### Просмотр сообщений ядра

Для просмотра сообщений ядра используется опция `-k` или `--dmesg`:

$ journalctl -kКопировать

Приведённая команда покажет все сообщения ядра для текущей загрузки. Чтобы просмотреть сообщения ядра для предыдущих сессий, нужно воспользоваться опцией `-b`:

$ journalctl -k -b -2Копировать

### Фильтрация по уровню ошибки

Во время диагностики и исправления неполадок в системе нередко требуется просмотреть логи и выяснить, есть ли в них сообщения о критических ошибках.

$ journalctl -p err -b 0Копировать

Приведённая команда покажет все сообщения об ошибках, имевших место в системе, с момента последней загрузки. В `journal` используется такая же классификация уровней ошибок, как и в `syslog`:

-   `emerg` — система неработоспособна
-   `alert` — требуется немедленное вмешательство
-   `crit` — критическое состояние
-   `err` — ошибка
-   `warning` — предупреждение
-   `notice` — следует обратить внимание
-   `info` — информационное сообщение
-   `debug` — отладочные сообщения

## Запись логов в стандартный вывод

По умолчанию `journalctl` использует для вывода сообщений логов внешнюю утилиту `less`. В этом случае к ним невозможно применять стандартные утилиты для обработки текстовых данных. Эта проблема решается легко:

$ journalctl --no-pagerКопировать

## Выбор формата вывода

C помощью опции `-o` можно преобразовывать данные логов в различные форматы, что облегчает их парсинг и дальнейшую обработку, например:

$ journalctl -u apache2.service -o jsonКопировать

Объект `json` можно представить в более структурированном и человекочитаемом виде, указав формат `json-pretty`:

$ journalctl -u apache2.service -o json-prettyКопировать

{
    "__CURSOR" : "s=39af8c988a2447198f83abd4ba184866;i=48cc;b=2ebfbe0d4ea74da7969703dd4409beeb...",
    "__REALTIME_TIMESTAMP" : "1577868308021968",
    "__MONOTONIC_TIMESTAMP" : "509869391",
    "_BOOT_ID" : "2ebfbe0d4ea74da7969703dd4409beeb",
    "PRIORITY" : "6",
    "SYSLOG_FACILITY" : "3",
    "SYSLOG_IDENTIFIER" : "systemd",
    "_TRANSPORT" : "journal",
    "_PID" : "1",
    "_UID" : "0",
    "_GID" : "0",
    "_COMM" : "systemd",
    "_EXE" : "/lib/systemd/systemd",
    "_CMDLINE" : "/sbin/init splash",
    "_CAP_EFFECTIVE" : "3fffffffff",
    "_SELINUX_CONTEXT" : "unconfined\n",
    "_SYSTEMD_CGROUP" : "/init.scope",
    "_SYSTEMD_UNIT" : "init.scope",
    "_SYSTEMD_SLICE" : "-.slice",
    "_MACHINE_ID" : "82c67903ca0f4f43b9b0083895c9505e",
    "CODE_FILE" : "../src/core/unit.c",
    "CODE_LINE" : "1718",
    "CODE_FUNC" : "unit_status_log_starting_stopping_reloading",
    "MESSAGE_ID" : "7d4958e842da4a758f6c1cdc7b36dcc5",
    "_HOSTNAME" : "web-server",
    "MESSAGE" : "Starting The Apache HTTP Server...",
    "UNIT" : "apache2.service",
    "INVOCATION_ID" : "0c74ebdfcbf04d57a23fd40df88859cc",
    "_SOURCE_REALTIME_TIMESTAMP" : "1577868308021921"
}Копировать

Помимо`json` данные логов могут быть преобразованы в следующие форматы:

-   `cat` — только сообщения из логов без служебных полей
-   `export` — бинарный формат, подходит для резервного копирования логов
-   `short` — формат вывода `syslog`
-   `short-iso` — формат вывода `syslog` с метками времени в формате ISO 8601
-   `short-monotonic` — формат вывода `syslog` c метками монотонного времени
-   `short-precise` — формат вывода `syslog` с метками точного времени
-   `verbose` — максимально подробный формат представления данных

## Прочие возможности

Опция `-n` используется для просмотра информации о недавних событиях в системе:

$ journalctl -nКопировать

По умолчанию на консоль выводится информация о последних 10 событиях. Но можно указать необходимое число событий:

$ journalctl -n 20Копировать

Для просмотра логов в режиме реального времени используется опция `-f`:

$ journalctl -fКопировать

## Управление логированием

Со временем объём логов растёт, и они занимают всё больше места на жёстком диске. Узнать объём имеющихся на текущий момент логов можно с помощью команды:

$ journalctl --disk-usage
Journals take up 18.0M on disk.Копировать

Настройка ротации логов осуществляется с помощью опций `--vacuum-size` и `--vacuum-time`. Первая из них устанавливает максимальный размер логов, а вторая — предельный срок хранения.

$ sudo journalctl --vacuum-size=1GКопировать

$ sudo journalctl --vacuum-time=1yearsКопировать

Настройки ротации логов можно также прописать в конфигурационном файле `/etc/systemd/journald.conf`:

$ sudo nano /etc/systemd/journald.confКопировать

[Journal]
# максимальный объём, который логи могут занимать на диске
SystemMaxUse=1GКопировать

Вместо редактирования глобального файла конфигурации можно переопределить заданные в нем значения:

$ sudo nano /etc/systemd/journald.conf.d/00-journal-size.confКопировать

[Journal]
# максимальный объём, который логи могут занимать на диске
SystemMaxUse=1GКопировать

Для применения изменений нужно перезапустить службу `journald`:

$ sudo systemctl restart systemd-journald.serviceКопировать

## Journald вместе с syslog (rsyslog)

События журнала могут быть переданы демону `syslog` двумя способами:

-   Перенаправлять все сообщения `systemd` в сокет `/run/systemd/journal/syslog`, где их может читать демон `syslog`. Это контролируется опцией `ForwardToSyslog` файла конфигурации `journald.conf`.
-   Демон `syslog` ведет себя как обычный клиент журнала и считывает сообщения из файлов журнала аналогично `journalctl`. Этот метод доступен только в том случае, если опция `Storage` не равна `none`.

Демон `syslog` по умолчанию использует второй способ, поэтому важна опция `Storage`, а не `ForwardToSyslog`.
