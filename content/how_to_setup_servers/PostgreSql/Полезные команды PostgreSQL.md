-   Как изменить root пароль в PostgreSQL?
```
$ /usr/local/pgsql/bin/psql postgres postgres Password: (oldpassword)  
ALTER USER postgres WITH PASSWORD ‘tmppassword’;  
$ /usr/local/pgsql/bin/psql postgres postgres  
Password: (tmppassword)
```

-   Как создать пользователя в PostgreSQL?

```
#CREATE USER ramesh WITH password ‘tmppassword’;  
CREATE ROLE
```


$ /usr/local/pgsql/bin/createuser sathiya  
Shall the new role be a superuser? (y/n) n  
Shall the new role be allowed to create databases? (y/n) n  
Shall the new role be allowed to create more new roles? (y/n) n  
CREATE ROLE

-   Как создать базу в PostgreSQL ?

1  
2  

# CREATE DATABASE mydb WITH OWNER ramesh;  
CREATE DATABASE

1  

/usr/local/pgsql/bin/createdb mydb -O ramesh CREATE DATABASE

-   Получаем список всех баз в Postgresql?

1  
2  
3  
4  
5  
6  
7  
8  
9  

# \l  
List of databases  
Name | Owner | Encoding  
———-+———-+———-  
backup | postgres | UTF8  
mydb | ramesh | UTF8  
postgres | postgres | UTF8  
template0 | postgres | UTF8  
template1 | postgres | UTF8

-   Как удалить базу в PostgreSQL?

1  
2  
3  
4  
5  
6  
7  
8  
9  
10  

# \l  
Name | Owner | Encoding  
———-+———-+———-  
backup | postgres | UTF8  
mydb | ramesh | UTF8  
postgres | postgres | UTF8  
template0 | postgres | UTF8  
template1 | postgres | UTF8  
# DROP DATABASE mydb;  
DROP DATABASE