Устанавливаем необходимые пакеты для сборки исходников

yum install -y make wget openssl-devel ncurses-devel  newt-devel libxml2-devel kernel-devel gcc gcc-c++ sqlite-devel libuuid-devel

[![](https://xaxatyxa.ru/wp-content/uploads/2013/08/make_wget_openssl-devel_ncurses-devel_newt-devel.jpg "make_wget_openssl-devel_ncurses-devel_newt-devel")](https://xaxatyxa.ru/wp-content/uploads/2013/08/make_wget_openssl-devel_ncurses-devel_newt-devel.jpg)

Переходим в каталог с иcходными кодами

cd /usr/src/

Скачиваем необходимые исходники:

wget http://downloads.asterisk.org/pub/telephony/dahdi-linux-complete/dahdi-linux-complete-current.tar.gz
wget http://downloads.asterisk.org/pub/telephony/libpri/libpri-1.4-current.tar.gz
wget http://downloads.asterisk.org/pub/telephony/asterisk/asterisk-11-current.tar.gz

Распаковываем

tar zxvf dahdi-linux-complete*
tar zxvf libpri*
tar zxvf asterisk*

Устанавливаем **DAHDI**

cd /usr/src/dahdi-linux-complete*
make && make install && make config

Вот так выглядит процесс сборки пакетов

[![](https://xaxatyxa.ru/wp-content/uploads/2013/10/cd_usr_src_dahdi-linux-complete_make-install_make-config.jpg "cd_usr_src_dahdi-linux-complete_make-install_make-config")](https://xaxatyxa.ru/wp-content/uploads/2013/10/cd_usr_src_dahdi-linux-complete_make-install_make-config.jpg)

DAHDI является сокращением от «Digium Asterisk Hardware Device Interface». DAHDI  позволяет использовать аппаратные средства (карты) для соединения Asterisk с традиционными аналоговыми или цифровыми телефонными сетями. Так как соответствующих карт нет, то по завершению установки мы увидим , что устройств не обнаружено:

DAHDI has been configured.
List of detected DAHDI devices:
No hardware found

[![](https://xaxatyxa.ru/wp-content/uploads/2013/10/cd_usr_src_dahdi-linux-complete_finish.jpg "cd_usr_src_dahdi-linux-complete_finish")](https://xaxatyxa.ru/wp-content/uploads/2013/10/cd_usr_src_dahdi-linux-complete_finish.jpg)

Поэтому если вы уверены что не будете использовать DATHI, то смело пропускайте его установку.

Устанавливаем **libpri**

cd /usr/src/libpri*
make && make install

[![](https://xaxatyxa.ru/wp-content/uploads/2013/10/cd_usr_src_libpri-make_install.jpg "cd_usr_src_libpri-make_install")](https://xaxatyxa.ru/wp-content/uploads/2013/10/cd_usr_src_libpri-make_install.jpg)

Устанавливаем **Asterisk**

cd /usr/src/asterisk*
./configure --libdir=/usr/lib64 && make menuselect && make && make install

Для 32 разрядной системы вторая строчка будет отличаться

./configure && make menuselect && make && make install

Нажимаем «Save & Exit»

[![](https://xaxatyxa.ru/wp-content/uploads/2013/08/configure-libdir-usr-lib64-make-menuselect.jpg "configure-libdir-usr-lib64-make-menuselect")](https://xaxatyxa.ru/wp-content/uploads/2013/08/configure-libdir-usr-lib64-make-menuselect.jpg)

Продолжаем установку

[![](https://xaxatyxa.ru/wp-content/uploads/2013/08/asterisk_make-samples_make-progdocs.jpg "asterisk_make-samples_make-progdocs")](https://xaxatyxa.ru/wp-content/uploads/2013/08/asterisk_make-samples_make-progdocs.jpg)

Устанавливаем примеры конфигурационных файлов  Asterisk

make samples

Устанавливаем  документацию Asterisk

make progdocs

Добавляем скрипт для старта Asterisk в папку /etc/init.d/

make config

Стартуем сервис DAHDI

service dahdi start

[![](https://xaxatyxa.ru/wp-content/uploads/2013/10/service_dahdi_start.jpg "service_dahdi_start")](https://xaxatyxa.ru/wp-content/uploads/2013/10/service_dahdi_start.jpg)

Стартуем сервис Asterisk

service asterisk start

[![](https://xaxatyxa.ru/wp-content/uploads/2013/10/service_asterisk_start-status.jpg "service_asterisk_start-status")](https://xaxatyxa.ru/wp-content/uploads/2013/10/service_asterisk_start-status.jpg)

Проверяем, что сервисы есть в автозагрузке системы

[![](https://xaxatyxa.ru/wp-content/uploads/2013/10/chkconfig-list-grep-asterisk-dahdi.jpg "chkconfig-list-grep-asterisk-dahdi")](https://xaxatyxa.ru/wp-content/uploads/2013/10/chkconfig-list-grep-asterisk-dahdi.jpg)

Если нет, то команда для добавления

chkconfig --add asterisk && chkconfig --add dahdi

Подключаемся к консоли Asterisk CLI

asterisk -rvvv

[![](https://xaxatyxa.ru/wp-content/uploads/2013/10/asterisk-rvvv.jpg "asterisk-rvvv")](https://xaxatyxa.ru/wp-content/uploads/2013/10/asterisk-rvvv.jpg)

**Возможные ошибки:**

**1. Ошибка при установке документации Asterisk (make progdocs)**

(cat contrib/asterisk-ng-doxygen; echo "HAVE_DOT=no"; \
        echo "PROJECT_NUMBER=11.5.0") | doxygen -
/bin/sh: line 1: doxygen: command not found
make: *** [progdocs] Error 127

[![](https://xaxatyxa.ru/wp-content/uploads/2013/08/contrib-asterisk-ng-doxygen.jpg "contrib-asterisk-ng-doxygen")](https://xaxatyxa.ru/wp-content/uploads/2013/08/contrib-asterisk-ng-doxygen.jpg)

Решение:

yum install doxygen

**2. Не запускается сервис asterisk (service asterisk start)**

asterisk: unrecognized service

[![](https://xaxatyxa.ru/wp-content/uploads/2013/08/asterisk-unrecognized-service.jpg "asterisk-unrecognized-service")](https://xaxatyxa.ru/wp-content/uploads/2013/08/asterisk-unrecognized-service.jpg)

решение: Ищите в тексте

> Добавляем скрипт для старта Asterisk в папку /etc/init.d/
> 
> make config

**3. При попытке подключиться к консоли Asterisk CLI (asterisk -rvvv) выдаёт ошибку «Unable to connect to remote asterisk (does /var/run/asterisk/asterisk.ctl exist?)»**

Как вариант, перезагрузка должна подействовать. Данную ошибку видим после свежей установки Asterisk. Происходит подобная ошибка потому что Asterisk запускается сразу, без предварительной настройки сервиса.


#asterisk
