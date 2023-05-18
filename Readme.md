### Запуск
В директории с проектом выполните команды:
```sh
mkdir target
docker-compose --env-file .env up -d
go build && ./observer
```
Перед запуском приложения убедитесь, что директория `path` из файла конфигурации `config.yml` существует.

### Описание
Проект использует пакет `github.com/fsnotify/fsnotify` для обработки событий в файловой системы. Когда происходят изменения в директории `path`, которая указана в файле конфигурации, пакет отправляет события, связанные с изменениями файлов, такие как Write, Create, Remove, Rename.

Перед началом мониторинга, происходит создание полной копии директории `path` в директорию `_backup`. При каждом изменении в целевой директории, происходит сравнение состояния файлов с актуальной версией из `_backup`. Изменения применяются к файлам в директории `_backup`, и при этом логируются в базу данных. Также выполняются команды, указанные в файле конфигурации.

Вы можете встретить такие конструкции, как:

```go
if fileExists(backupFile) {
    continue
}
```
Они необходимы, так как событие Write может генерировать события Create и Remove, в зависимости от файловой системы.

События Write отражают изменения содержимого файлов. С использованием пакета `github.com/sergi/go-diff/diffmatchpatch` происходит сравнение версии из `_backup` и актуальной версии файла. Этот пакет предоставляет удобный формат для представления изменений в файлах.
