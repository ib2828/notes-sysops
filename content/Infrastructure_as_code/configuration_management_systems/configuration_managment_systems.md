Для первоначальной настройки чистого сервера можно добавить такие временные настройки в инвентори файл, что бы по быстрому создать пользователей
Эти настройки сразу же надо удалить из файла
ansible_become_password: pass1
ansible_user: user1
ansible_ssh_pass: pass1

аргумент что бы не проверять отпечаток ssh ключа

--ssh-common-args='-o StrictHostKeyChecking=no'



