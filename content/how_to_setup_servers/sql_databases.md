```CREATE DATABASE `zabbix` CHARACTER SET utf8 COLLATE utf8_general_ci;```
```GRANT ALL PRIVILEGES ON zabbix.* TO 'zabbix'@'localhost' IDENTIFIED BY 'password' WITH GRANT OPTION;```


```UPDATE user SET password=PASSWORD("password") where User='zabbix';```


```mysqladmin -u root password 'НОВЫЙ_ПАРОЛЬ'```
```mysqladmin -u root -p'ТЕКУЩИЙ_ПАРОЛЬ' password 'НОВЫЙ_ПАРОЛЬ'```



```mysqldump -uroot -p database > database.sql```





```CREATE DATABASE `zabbix` CHARACTER SET utf8 COLLATE utf8_general_ci;```
```GRANT ALL PRIVILEGES ON zabbix.* TO 'zabbix'@'localhost' IDENTIFIED BY 'password' WITH GRANT OPTION;```










###############################################################################################################
###############################################################################################################
###############################################################################################################
###############################################################################################################
###############################################################################################################
###############################################################################################################
###############################################################################################################
###############################################################################################################
###############################################################################################################




### Table of Contents </br>
- [Создание read-only пользователя в PostgreSQL](#postgresql_create_readonly_user)
- [Создание dump-а БД](#postgresql_pgdump)
- [Восстановление dump-а БД](#postgresql_pgdump_restore)
- [Зачистка wal логов PostgreSQL](#postgresql_pg_resetwal)
- [Проверка списка доступных расширений](#postgresql_show_available_extensions)
- [Liquibase — Waiting for changelog lock….](#liquibase_lock)


##### Создание пользователя и базы </br>
123
3232
3232

--- 
##### Создание read-only пользователя в PostgreSQL <a name="postgresql_create_readonly_user"></a> </br>
Источник: https://gist.github.com/oinopion/4a207726edba8b99fd0be31cb28124d0  </br>
</br>
Create a group </br>
```CREATE ROLE readaccess;```
</br>
Grant access to existing tables </br>
```GRANT USAGE ON SCHEMA public TO readaccess;```</br>
```GRANT SELECT ON ALL TABLES IN SCHEMA public TO readaccess;```</br>
</br>
Grant access to future tables </br>
```ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT ON TABLES TO readaccess;```
</br>
Create a final user with password </br>
```CREATE USER user WITH PASSWORD 'secret';```
```GRANT readaccess TO user;```
</br>

---
##### Создание dump-а БД<a name="postgresql_pgdump"></a></br>
```sudo -Hu postgres pg_dump -d dbname | bzip2 > dbdump.sql.bz2```
</br>

---
##### Восстановление dump-а БД<a name="postgresql_pgdump_restore"></a></br>
```bzip2 -d dbdump.sql.bz2 |sudo -Hu postgres psql dbname```
</br>

---
##### Зачистка wal логов PostgreSQL<a name="postgresql_pg_resetwal"></a></br>
Нужно остановить сервер PostgreSql</br>
Выполняем команду pg_controldata указывая путь до базы postgresql</br>
```/usr/lib/postgresql-8.4/bin/pg_controldata /opt/backup/postgresql/8.4/data/```</br>
нас интересуют строчки:</br>
```Latest checkpoint's NextXID: 0/1186399159```</br>
```Latest checkpoint's NextOID: 4716704```</br>
Выполняем команду pg_resetwal коророй указываем NextOID и NextXID (команда выполняется из под пользователя postgres)</br>
```pg_resetwal  -o 4716704 -x 1186399159 -f /var/lib/pgpro/std-12/data/```</br>
Запускаем сервер PostgreSql</br>
</br>

---
##### Проверка списка доступных расширений <a name="postgresql_show_available_extensions"></a></br>
```postgres$ psql -c 'SELECT name, comment FROM pg_available_extensions ORDER BY name;'```
</br>

---
##### Liquibase — Waiting for changelog lock….<a name="liquibase_lock"></a></br>
Following SQL query returns locked column as true.</br>
```SELECT * FROM DATABASECHANGELOGLOCK;```</br>
To remove database lock:</br>
```UPDATE DATABASECHANGELOGLOCK SET locked=false, lockgranted=null, lockedby=null WHERE id=1;```




================================================================================</br>
================================================================================</br>
================================================================================</br>
================================================================================</br>
================================================================================</br>
================================================================================</br>
================================================================================</br>
================================================================================</br>



Killing Idle Sessions
```
SELECT pg_terminate_backend(pg_stat_activity.pid)
FROM pg_stat_activity
WHERE pg_stat_activity.datname = '[your database name goes here]'
  AND pid <> pg_backend_pid();
```




Основное
Инициализация БД
postgresql-setup initdb
или:

sudo su - postgres -c "initdb -E UTF8 -D '/var/lib/pgsql/data'"
Поддержка русского:
В файл /var/lib/pgsql/data/postgresql.conf добавляем:

client_encoding = UTF8
Доступ по паролю:
vi /var/lib/pgsql/data/pg_hba.conf
должно быть так (host .. md5):

host    all             all             127.0.0.1/32            md5
host    all             all             ::1/128                 md5
Подключиться в консоли:
sudo -u postgres psql
Создать пользователя и БД(с поддержкой русского):
CREATE USER jira WITH password 'jira123';
CREATE DATABASE jira5db WITH OWNER jira ENCODING 'UTF8' TEMPLATE = template0;
GRANT ALL privileges ON DATABASE jira5db TO jira;
Посмотреть список баз данных:
\l
Подключиться к БД:
\connect database_name
Список таблиц:
\dt *
Описание таблиц

\d *
Удалить БД:
DROP DATABASE jira5db;
Дамп БД:
pg_dump -c -h 192.168.0.1 -U test_user test_database > ./dump.sql
Залить дамп:
cat dump.sql | psql -h 192.168.0.1 test_database test_user
Если при создании БД выдается ошибка createdb: database creation failed: ERROR: new encoding (UTF8) is incompatible with the encoding of the template database (SQL_ASCII)
Все БД создаются из темплейтов, в данном случае у нас просто нет темплейта для UTF8 - переделаем существующий template1 на UTF8:

UPDATE pg_database SET datistemplate = FALSE WHERE datname = 'template1';
DROP DATABASE template1;
CREATE DATABASE template1 WITH TEMPLATE = template0 ENCODING = 'UNICODE' LC_COLLATE = 'en_US.UTF-8' LC_CTYPE = 'en_US.UTF-8';
UPDATE pg_database SET datistemplate = TRUE WHERE datname = 'template1';


###############################################################################################################
### PostgreSQL 2020 </br>

- [01. Реляционные базы, история и место в современном мире](#course_psql_2020_item1)
- [02. SQL и реляционные СУБД. Введение в PostgreSQL](#course_psql_2020_item1)
- [03. Физический уровень PostgreSQL](#course_psql_2020_item1)
- [04. Установка PostgreSQL](#course_psql_2020_item1)
- [05. Настройка PostgreSQL](#course_psql_2020_item1)
- [06. Логический уровень PostgreSQL](#course_psql_2020_item1)
- [07. Резервное копирование и восстановление](#course_psql_2020_item1)
- [08. Виды и устройство репликации в PostgreSQL. Практика применения](#course_psql_2020_item1)
- [09. Многоверсионность](#course_psql_2020_item1)
- [10. Журналы](#course_psql_2020_item1)
- [11. Блокировки](#course_psql_2020_item1)
- [12. Виды индексов. Работа с индексами и оптимизация запросов](#course_psql_2020_item1)
- [13. Различные виды join'ов. Применение и оптимизация](#course_psql_2020_item1)
- [14. Сбор и использование статистики](#course_psql_2020_item1)
- [15. Работа с большим объемом реальных данных](#course_psql_2020_item1)
- [16. Варианты кластеров высокой доступности для PostgreSQL](#course_psql_2020_item1)
- [17. Работа с кластером высокой доступности](#course_psql_2020_item1)
- [18. Способы горизонтального масштабирования PostgreSQL](#course_psql_2020_item1)
- [19. Docker и контейнеры](#course_psql_2020_item1)
- [20. Основы Kubernetes](#course_psql_2020_item1)
- [21. Работа с горизонтально масштабируемым кластером](#course_psql_2020_item1)
- [22. PostgreSQL и Kubernetes](#course_psql_2020_item1)
- [23. PostgreSQL и Google Cloud Platform](#course_psql_2020_item1)
- [24. PostgreSQL и AWS](#course_psql_2020_item1)
- [25. PostgreSQL и Azure](#course_psql_2020_item1)
###############################################################################################################
### DBA1-10 «Администрирование PostgreSQL 10. Базовый курс»</br>
- [     Введение](#course_postgrespro_dba1_introduction)
- [1.	Установка и управление сервером](#course_postgrespro_dba1_item_1)
- [2.	Использование psql](#course_postgrespro_dba1_item_2)
- [3.	Конфигурирование](#course_postgrespro_dba1_item_3_)
- [4.	Общее устройство PostgreSQL](#course_postgrespro_dba1_item_4_)
- [5.	Изоляция и многоверсионность](#course_postgrespro_dba1_item_5_)
- [6.	Буферный кэш и журнал](#course_postgrespro_dba1_item_6_)
- [7.	Базы данных и схемы](#course_postgrespro_dba1_item_7_)
- [8.	Системный каталог](#course_postgrespro_dba1_item_8_)
- [9.	Табличные пространства](#course_postgrespro_dba1_item_9_)
- [10.	Низкий уровень](#course_postgrespro_dba1_item_10)
- [11.	Мониторинг](#course_postgrespro_dba1_item_11)
- [12.	Сопровождение](#course_postgrespro_dba1_item_12)
- [13.	Роли и атрибуты](#course_postgrespro_dba1_item_13)
- [14.	Привилегии](#course_postgrespro_dba1_item_14)
- [15.	Политики защиты строк](#course_postgrespro_dba1_item_15)
- [16.	Подключение и аутентификация](#course_postgrespro_dba1_item_16)
- [17.	Резервное копирование](#course_postgrespro_dba1_item_17)
- [18.	Репликация](#course_postgrespro_dba1_item_18)
</br>

---
#### Введение <a name=course_postgrespro_dba1_introduction></a></br>
---
#### 1.	Установка и управление сервером <a name=course_postgrespro_dba1_item_1></a></br>
---
#### 2.	Использование psql <a name=course_postgrespro_dba1_item_2></a></br>
---
#### 3.	Конфигурирование <a name=course_postgrespro_dba1_item_3_></a></br>
---
#### 4.	Общее устройство PostgreSQL <a name=course_postgrespro_dba1_item_4_></a></br>
---
#### 5.	Изоляция и многоверсионность <a name=course_postgrespro_dba1_item_5_></a></br>
---
#### 6.	Буферный кэш и журнал <a name=course_postgrespro_dba1_item_6_></a></br>
---
#### 7.	Базы данных и схемы <a name=course_postgrespro_dba1_item_7_></a></br>
---
#### 8.	Системный каталог <a name=course_postgrespro_dba1_item_8_></a></br>
---
#### 9.	Табличные пространства <a name=course_postgrespro_dba1_item_9_></a></br>
---
#### 10. Низкий уровень <a name=course_postgrespro_dba1_item_10></a></br>
---
#### 11. Мониторинг <a name=course_postgrespro_dba1_item_11></a></br>
---
#### 12. Сопровождение <a name=course_postgrespro_dba1_item_12></a></br>
---
#### 13. Роли и атрибуты <a name=course_postgrespro_dba1_item_13></a></br>
---
#### 14. Привилегии <a name=course_postgrespro_dba1_item_14></a></br>
---
#### 15. Политики защиты строк <a name=course_postgrespro_dba1_item_15></a></br>
---
#### 16. Подключение и аутентификация <a name=course_postgrespro_dba1_item_16></a></br>
---
#### 17. Резервное копирование <a name=course_postgrespro_dba1_item_17></a></br>
---
#### 18. Репликация <a name=course_postgrespro_dba1_item_18></a></br>
---














Полезные команды MySQL
MySQL
Авторизация на сервере (из консоли), -h при необходимости авторизации на удалённом сервере
mysql -h hostname -u root -p
Создание БД
mysql> create database `databasename`;
Создание БД с указанием необходимой кодировки
mysql> create database `databasename` default character set 'utf8' collate 'utf8_unicode_ci';
Получить список всех БД на сервере
mysql> show databases;
Переключится на БД
mysql> use `db name`;
Получить список таблиц в базе
mysql> show tables;
Посмотреть структуру таблицы
mysql> describe `table name`;
Ещё один вариант
mysql> show columns from `table name`;
Удалить БД
mysql> drop database `database name`;
Удалить таблицу
mysql> drop table `table name`;
Показать все данные в таблице
mysql> SELECT * FROM `table name`;
Показать строки, где поле `field name` имеет значение "whatever".
mysql> SELECT * FROM `table name` WHERE `field name` = 'whatever';
Показать строки с именем "Bob" и номеном "3444444"
mysql> SELECT * FROM `table name` WHERE name = 'Bob' AND phone_number = 3444444;
Показать строки с номером "3444444" не содержащие имени "Bob" отсортированные по номеру.
mysql> SELECT * FROM `table name` WHERE name != 'Bob' AND phone_number = 3444444 order by phone_number;
Показать записи с именем, начинающимся на "bob" и номером 3444444
mysql> SELECT * FROM `table name` WHERE name like 'Bob%' AND phone_number = 3444444;
Верннуть все данные с именем, начинающемся на "bob" и номером 3444444 ограничить вывод пятью первыми строками
mysql> SELECT * FROM `table name` WHERE name like 'Bob%' AND phone_number = 3444444 limit 0,5;
Используем регулярное выражение. Для регистрозависимого выбора используйте "REGEXP BINARY". Данный запрос найдёт все записи, начинающиеся на "a"
mysql> SELECT * FROM `table name` WHERE rec RLIKE '^a';

Показать уникальные записи
mysql> SELECT DISTINCT `column name` FROM `table name`;
Показать выбранные колонки отсортированные от а до я (ASC) или от я до а (DESC)
mysql> SELECT `col1`,`col2` FROM `table name` ORDER BY `col2` DESC;
Вернуть количество строк в таблице.
mysql> SELECT COUNT(*) FROM `table name`;
Просуммировать все числовые поля таблицы
mysql> SELECT SUM(*) FROM `table name`;
Объединение таблиц. Как работает JOIN (в картинках)
mysql> select lookup.illustrationid, lookup.personid,person.birthday from lookup left join person on lookup.personid=person.personid=statement to join birthday in person table with primary illustration id;
Создание пользователя. Вход под root. Переключение на БД mysql. Создание пользователя и обновление привилегий.
mysql -u root -p
mysql> use mysql;
mysql> INSERT INTO user (Host,User,Password) VALUES('%','username',PASSWORD('password'));
mysql> flush privileges;
Смена пароля пользователя из консоли
mysqladmin -u username -h hostname -p password 'new-password'
Смена пароля пользователя из консоли MySQL. Вход как root. Смена пароля. Обновление привелегий.
mysql -u root -p
mysql> SET PASSWORD FOR 'user'@'hostname' = PASSWORD('passwordhere');
mysql> flush privileges;
Восстановление пароля root пользователя. Остановить MySQL сервер. Запустить с пониженной безопасностью. Залогинится на MySQL как root. Установить новый пароль. Разлогинится и перезапустить MySQL сервер.
/etc/init.d/mysql stop
mysqld_safe --skip-grant-tables &amp;
mysql -u root
mysql> use mysql;
mysql> update user set password=PASSWORD('newrootpassword') where User='root';
mysql> flush privileges;
mysql> quit
/etc/init.d/mysql stop
/etc/init.d/mysql start
Установка пароля root если он ещё не задавался ранее
mysqladmin -u root password newpassword
Смена пароля root
mysqladmin -u root -p oldpassword newpassword
Разрешить пользователю "Bob" подключаться к серверу c локального адреса с паролем "passwd". Войти как root. Переключиться на БД mysql. Дать привилегии. Обновить привелегии.
mysql -u root -p
mysql> use mysql;
mysql> grant usage on *.* to bob@localhost identified by 'passwd';
mysql> flush privileges;
Предоставить пользователю привилегии на БД. Авторизоваться как root. Переключиться на БД mysql. Предоставить привилегии. Обновить кеш привилегий.
mysql -u root -p
mysql> use mysql;
mysql> INSERT INTO user (Host,Db,User,Select_priv,Insert_priv,Update_priv,Delete_priv,Create_priv,Drop_priv) VALUES ('%','databasename','username','Y','Y','Y','Y','Y','N');
mysql> flush privileges;
или
mysql> grant all privileges on databasename.* to username@localhost;
mysql> flush privileges;
Обновить информацию для существующего пользователя
mysql> use mysql;
mysql> UPDATE `user` SET Select_priv = 'Y',Insert_priv = 'Y',Update_priv = 'Y' where `User` = 'user';
flush privileges;
Удалить строки из таблицы
mysql> DELETE from `table name` where `field_name` = 'whatever';
Обновить кеш привилегий
mysql> flush privileges;
Удалить колонку из таблицы
mysql> alter table `table name` drop column `column name`;
Добавить колонку в таблицу
mysql> alter table `table name` add column `new column name` varchar (20);
Переименовать колонку
mysql> alter table `table name` change `old column name` `new column name` varchar (50);
Сделать данные в колоке уникальными (если дублирующиеся уже есть - будет ошибка)
mysql> alter table `table name` add unique (`column name`);
Модифицировать колонку
mysql> alter table `table name` modify `column name` VARCHAR(3);
Удалить индекс
mysql> alter table `table name` drop index `colmn name`;
Загрузить данные в БД из CSV файла.
mysql> LOAD DATA INFILE '/tmp/filename.csv' replace INTO TABLE `table name` FIELDS TERMINATED BY ',' LINES TERMINATED BY '\n' (field1,field2,field3);
Сделать дамп всех БД для бэкапа. Бэкап это файл с SQL командами для воссоздания всех баз.
mysqldump -u root -p --opt > /tmp/alldatabases.sql
Сделать дамп одной базы.
mysqldump -u username -p --databases databasename > /tmp/databasename.sql
Сделать дамп одной таблицы
mysqldump -c -u username -p databasename tablename > /tmp/databasename.tablename.sql
Восстановить БД (или таблицу) из бэкапа
mysql -u username -p databasename &lt; /tmp/databasename.sql
Создание таблицы, пример 1.
mysql> CREATE TABLE `table name` (
 `firstname` VARCHAR(20),
 `middleinitial` VARCHAR(3),
 `lastname` VARCHAR(35),
 `suffix` VARCHAR(3),
 `officeid` VARCHAR(10),
 `userid` VARCHAR(15),
 `username` VARCHAR(8),
 `email` VARCHAR(35),
 `phone` VARCHAR(25),
 `groups` VARCHAR(15),
 `datestamp` DATE,
 `timestamp` time,
 `pgpemail` VARCHAR(255)
);
Создание таблицы, пример 2.
mysql> CREATE TABLE `table name` (
 personid int(50) not null auto_increment primary key,
 firstname VARCHAR(35),
 middlename VARCHAR(50),
 lastname VARCHAR(50) default 'bato'
);