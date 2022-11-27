Начиная с версии 5.0.60 binlog включён по умолчанию и не слабо кушает место на винте. Можно сделать 2 вещи:  
  
Уменьшить занимаемое логами место:
`korp` `# nano /etc/mysql/my.cnf`

В секции [mysqld] добавить строку
`expire_logs_days = 5`

Или отключить запись лога вообще
`korp` `# nano /etc/mysql/my.cnf`

В секции [mysqld] закоментировать строку
`log-bin`

Ну и не забываем после этого перезапустить MySQL
`korp` `# /etc/init.d/mysql restart`



#mysql/binlog