#### Генерация новой пары ключей
```gpg --gen-key```
#### Просмотр ключей
Просмотр публичных ключей
```gpg -k```
Просмотр приватных ключей
```gpg -K```
#### Экспорт ключей
Экспорт публичных ключей
```gpg --export --armor <KeyId> > public.gpg```
Экспорт приватных ключей
```gpg --export-secret-key --armor <KeyId> > secret.gpg```
#### Импорт ключей
Импорт публичных ключей
```gpg --import public.gpg```
Импорт приватных ключей
```gpg --import secret.gpg```
#### Удаление ключей
Экспорт публичных ключей
```gpg --delete-keys <KeyId>```
Экспорт приватных ключей
```gpg --delete-secret-key <KeyId>```



Итак, что же означают все эти странные последние строки?

rsa — Алгоритм шифрования RSA.
2048 — Длина ключа.
1970-01-01 — Дата создания ключа.
2BB680...E426AC — Отпечаток ключа. Его следует сверять при импортировании чужого публичного ключа — у обоих сторон он должен быть одинаков.
uid — Идентификатор (User-ID).
pub и sub — Типы ключа:

pub — Публичный ключ.
sub — Публичный подключ.
sec — Секретный ключ.
ssb — Секретный подключ.

[SC] и [E] — Предназначение каждого ключа. Когда вы создаёте ключ, вы получаете аж 4 криптоключа: для шифрования, расшифровки, подписи и проверки подписи:

S — Подпись (Signing).
C — Подпись ключа (Certification). Об этом пойдёт речь чуть позже.
E — Шифрование (Encryption).
A — Авторизация (Authentication). Может использоваться, например, в SSH.

Ключи

Главный компонент GnuPG — ключ. Он состоит из двух частей: приватной и публичной. Приватная хранится только у владельца ключа в тщательно запароленном виде. Публичная распространяется свободно в интернетах.

Приватный ключ используется для:

    подписывания данных;
    дешифрования зашифрованных файлов.

Во всех операциях, где используется приватный ключ, обязательно нужен вводить пароль к приватному ключу (дальше этот пароль называется: парольная фраза).

Публичный ключ используется для:

    проверки подписи данных;
    шифрования данных.
Таким образом, если вы хотите кому-то отправить зашифрованный текст, который получатель сможет расшифровать, вы берёте публичный ключ этого пользователя, шифруете этим ключом данные, отправляете. Получатель берёт приватную компоненту ключа и расшифровывает.

А если вы хотите подтвердить подлинность текста, вы берёте свой приватный ключ, вычисляете при помощи него и первоначального текста специальный набор байтов, называемый подписью, и отправляете подпись вместе с документом. Получатель берёт ваш публичный ключ, текст и подпись, скармливает это всё специальному алгоритму, который говорит: «да, эта подпись соответствует этому публичному ключу для этого текста». Таким образом получатель может быть уверен, что он получил точно тот же документ, который вы ему отправили.

#gpg
