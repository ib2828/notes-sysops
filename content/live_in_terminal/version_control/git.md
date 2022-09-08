#### Структура файлов Git в проекте <a name="git_structure_project"></a>
Cтруктура каждого проекта, работающего под управлением Git, состоит из трех проектов:
+ служебная директория (git directory), хранилище метаданных и базу данных всех объектов проекта.
+ рабочая директория (working directory), папка, в которой  файлы твоего проекта.
+ область подготовленных файлов (staging area). Под областью подготовленных файлов подразумевается обычный файл, расположенный в служебной директории и содержащий информацию об изменениях, которые войдут в следующий коммит.

#git/structure

---
#### Работа с репозиториями
Чтобы склонировать репозиторий с SSH-ключами, отличными от ключей по умолчанию, можно воспользоваться следующим подходом:
```git -c core.sshCommand="ssh -i ~/.ssh/another_id_rsa" clone host:repo.git```
После клонирования можно установить сменить ключи для этого репозитория на постоянной основе:
```git config core.sshCommand "ssh -i ~/.ssh/another_id_rsa"``` </br>

#git/clone

---
#### Работа с ветками
Удаление всех локальных веток кроме мастера.
```git branch | grep -v "master" | xargs git branch -D```</br>

#git/branch

---
#### Работа с коммитами
You cannot push commits for ''. You can only push commits that were committed with one of your own verified emails.
```git commit --amend --reset-author --no-edit```</br>
</br>
Форматированнный вывод истории коммитов.</br>
```git log --graph --abbrev-commit --decorate --all --format=format:"%C(bold blue)%h%C(reset) - %C(bold cyan)%aD%C(dim white) - %an%C(reset) %C(bold green)(%ar)%C(reset)%C(bold yellow)%d%C(reset)%n %C(white)%s%C(reset)"```</br>

#git/commit

---
#### Работа с тэгами
Добавление тэгов
```git tag -a tagName -m "comment"```
```git push --tags```
Удаление тэгов
```git tag -d tagName```
```git push --delete origin tagName```

#git/tag

----------------------------------------------------------------

#git

