Настройка СУБД Postgresql для аутентификации пользователей через Active Directory

[[исправить](https://www.opennet.ru/tips/3212_postgresql_kerberos_ldap_activedirectory_auth.shtml#top "Общедоступная правка в wiki-стиле")]

В статье расскажу про мой опыт настройки СУБД Postgresql для включения
аутентификации пользователей через Active Directory с помощью протокола GSSAPI.

Предполагается, что домен Active Directory и БД Postgresql уже развёрнуты.

Для примера у меня развёрнут тестовый стенд со следующими параметрами:

-   Сервер с Active Directory: Windows Server 2022
    
-   Функциональный уровень домена: Windows Server 2016
    
-   Имя домена: domain.test
    
-   Контроллер домена: dc.domain.test
    
-   Клиентский компьютер с Windows 11, присоединённый к домену
    
-   Сервер с БД Postgresql 13 на Debian 11
    
-   DNS имя сервера СУБД: pg-host.domain.test
    
    **Установка пакетов на сервере СУБД**
    
    Для систем на базе Debian:
    
       root# apt install krb5-user postgresql
    **Настройка Kerberos на сервере СУБД**
    
    В файле /etc/krb5.conf на сервере с СУБД Postgresql добавляем описание для
    области Kerberos домена Windows:
    
       [libdefaults]
       default_realm = DOMAIN.TEST
       ...
       [realms]
       DOMAIN.TEST = {
         kdc = dc.domain.test
         admin_server = dc.domain.test
       }
    Проверяем, что можем получить билет:
    
       user@pg-host:~$ kinit Administrator@DOMAIN.TEST
    Смотрим список полученных билетов на сервере с БД:
    
       user@pg-host:~$ klist
       Ticket cache: FILE:/tmp/krb5cc_0
       Default principal: Administrator@DOMAIN.TEST
       Valid starting Expires Service principal
       23.07.2022 20:55:44 24.07.2022 06:55:44 krbtgt/DOMAIN.TEST@DOMAIN.TEST
       renew until 24.07.2022 20:55:38
    **Настройка описания имени службы в Active Directory**
    
    **Настройка пользователя Active Directory**
    
    Для того, чтобы пользователи могли подключаться к СУБД с помощью GSSAPI, в
    Active Directory должна быть учётная запись с соответствующей записью
    уникального описания службы в поле Service Principal Name и User Principal
    Name: servicename/hostname@REALM.
    
    Значение имени сервиса servicename по умолчанию postgres и может быть изменено
    во время сборки Postgresql с помощью параметра with-krb-srvnam
    
       ./configure --with-krb-srvnam=whatever
    
-   hostname - это полное доменное имя сервера, где работает СУБД
    (pg-host.domain.test) оно должно быть зарегистрировано в DNS сервере, который
    использует Active Directory.
    
-   realm - имя домена (DOMAIN.TEST)
    
    В моём примере имя службы получается: postgres/pg-host.domain.test@DOMAIN.TEST
    
    Создаём пользователя pg-user в Active Directory, указываем "Запретить смену
    пароля пользователей" и "Срок действия пароля не ограничен".
    
    **Создание файла с таблицами ключей**
    
    Для того, чтобы служба СУБД могла подключаться к Active Directory без ввода
    пароля, необходимо создать файл keytab на сервере с Windows Server и после
    переместить его на сервер c СУБД.
    
    Создание файла выполняется с помощью команды ktpass.exe:
    
       ktpass.exe -princ postgres/pg-host.domain.test@DOMAIN.TEST -ptype KRB5_NT_PRINCIPAL -crypto ALL -mapuser pg-user@domain.test -pass <пароль> -out %tmp%\krb5.keytab
    Эта же команда выполняет привязку имени сервиса к учётной записи.
    
    Подключаемся к СУБД Postgresql и определяем, где СУБД предполагает наличие файла keytab:
    
       postgres@pg-host:~$ psql
       postgres=# show krb_server_keyfile;
       FILE:/etc/postgresql-common/krb5.keytab
    Копируем файл с Windows Server на сервер с СУБД в указанное место.
    
    Если файл необходимо расположить в другом месте, то необходимо поменять
    параметр krb_server_keyfile:
    
       sql> alter system set krb_server_keyfile=''/path'';
    **Настройка Postgresql**
    
    Настройка файла доступа pg_hba.conf и файла сопоставления имён pg_ident.conf
    
    В файле pg_ident.conf описываем сопоставление пользователей Active Directory с пользователями БД:
    
    # MAPNAME SYSTEM-USERNAME PG-USERNAME
       gssmap /^(.*)@DOMAIN\.TEST$ \1
    Данное сопоставление указывает отображать доменного пользователя
    user@DOMAIN.TEST в пользователя БД user (для подключения, пользователь user уже
    должен быть создан в БД).
    
    В файле pg_hba.conf указываем, например, что использовать аутентификацию с
    помощью GSSAPI необходимо только для пользователей, состоящих в группе
    krb_users и подключающихся из сети 192.168.1.0/24:
    
       host all +krb_users 192.168.1.0/24 gss include_realm=1 krb_realm=DOMAIN.TEST map=gssmap
    Здесь:
    
-   map=gssmap - имя сопоставления из файла pg_ident.conf
    
-   krb_realm - имя домена Active Directory
    
    **Создание пользователей**
    
    Создаём пользователей в Active Directory: user1, user2.
    
    Создаём пользователей в СУБД:
    
        postgres=# create role user1 login;
        postgres=# create role user2 login;
        postgres=# create role user3 login encrypted password ''пароль'' ;
    Создаём группу krb_users (как файле pg_hba.conf) и добавляем необходимых пользователей в неё:
    
        postgres=# grant krb_users to user1;
        postgres=# grant krb_users to user2;
    В данном случае, пользователи user1, user2 смогут подключится к СУБД через
    GSSAPI, используя учётные данные из Active Directory, а пользователь user3
    сможет подключиться только с указанием пароля, хранящимся в БД.
    
    **Проверка подключения**
    
    На Windows машине проверяем подключение.
    
    Входим на компьютер с Windows через Active Directory, например, как user1@domain.test.
    
    Запускаем клиент postgresql, в строке подключения указываем полное доменное имя
    сервера СУБД, как прописано в файле keytab - pg-host.domain.test, логин и
    пароль не указываем:
    
       C:\> chcp 1251
       C:\> psql -h pg-host.domain.test -d postgresql
    Смотрим список билетов Kerberos:
    
       C:\> klist.exe
       #2> Клиент: user1 @ DOMAIN.TEST
       Сервер: postgres/pg-host.domain.test @ DOMAIN.TEST
       Тип шифрования KerbTicket: RSADSI RC4-HMAC(NT)
       флаги билета 0x40a10000 -> forwardable renewable pre_authent name_canonicalize
       Время начала: 7/24/2022 11:22:54 (локально)
       Время окончания: 7/24/2022 20:59:53 (локально)
       Время продления: 7/24/2022 10:59:53 (локально)
       Тип ключа сеанса: RSADSI RC4-HMAC(NT)
       Флаги кэша: 0
       Вызванный центр распространения ключей: dc.domain.test
    Проверяем подключение пользователя user3 с помощью пароля:
    
       C:\> psql -h pg-host.domain.test -d postgresql -U user3
    Как видно аутентификация в СУБД работает успешно как с помощью Active
    Directory, так и через пароль, хранящийся в БД.

#postgresql
#postgresql/Active_Directory
#kerberos

