# Использование Docker в первый раз
Нужно добавить пользователя в группу docker:
```bash
usermod -aG docker $USER
```
После чего стоит перезагрузить систему, чтобы изменения вступили в силу.
В противном случае будет такая ошибка:
```
ERROR: permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Head "http://%2Fvar%2Frun%2Fdocker.sock/_ping": dial unix /var/run/docker.sock: connect: permission denied
```
# Сборка
```bash
docker build -t [HOST[:PORT_NUMBER]/]PATH[:TAG] <Dockerfile_folder_path>
```
Собирается образ из `Dockerfile`, который ищется по пути `<Dockerfile_folder_path>`.
# Просмотр списка собранных образов
```bash
docker images --all
```
# Запуск
```bash
docker run -it --rm <image_name> sh
```
Здесь происходит запуск контейнера по имени образа (`<image_name>`). Флаги:
- `-i` интерактивный режим
- `-t` виртуальный `tty`
- `--rm` очистка контейнера и его файловой системы после завершения работы (не затрагивает образ)
- `sh` запускает стандартный `Shell` внутри
Убедиться в том, что всё очистилось можно с помощью команды `docker ps -a`. Там не будет работающего контейнера, который был запущен с `--rm`.
# Повторный запуск
Если контейнер завершил свою работу (`Exited(0)`), то можно заново запустить его командой:
```bash
docker start <container_name> -ai
```
Флаги:
- `-a` attach
- `-i` интерактивный.
# Просмотр контейнеров
```bash
docker ps --all
```
# Коммит
```bash
docker container commit -m "<Message>" <container_name> <new_image_name>
```
# Удаление образа
```bash
docker rmi <image_name>
```
# Удаление контейнера
```bash
docker rm <container_name>
```
# Очистка висячих образов, контейнеров, томов и сетей
```bash
docker system prune
```
# Очистка остановленных контейнеров
```bash
docker container prune
```

---
# Создание отдельных образов по слоям
Если вам нужно получить **отдельный независимый Docker-образ на каждый промежуточный слой** (например, чтобы развернуть или сохранить каждый шаг отдельно), это можно сделать несколькими путями.
## Способ 1. Пошаговая сборка с сохранением тегов (ручной или скриптом)
Вы можете разбить ваш `Dockerfile` на несколько файлов, где каждый последующий файл наследуется (`FROM`) от предыдущего собранного образа.
- **Создайте Dockerfile.step1**:   
```dockerfile
FROM ubuntu:22.04
RUN apt-get update && apt-get install -y curl
```    
Собираем образ:
```bash
docker build -t my-image-layer1 -f Dockerfile.step1 .
```
- **Создайте Dockerfile.step2**, используя первый образ как базу:
```dockerfile
FROM my-image-layer1
RUN apt-get install -y git
```
Собираем второй образ:
```bash
docker build -t my-image-layer2 -f Dockerfile.step2 .
```
## Способ 2. Автоматическое извлечение слоев через `docker history` и создание образов

Каждый слой в Docker имеет свой уникальный ID (`IMAGE` или `CACHED` / digest в истории сборки). Вы можете посмотреть историю образа и собрать из каждого промежуточного слоя отдельный тег/образ.
- Соберите обычный `Dockerfile` до конца:
```bash
docker build -t full-app .
```
- Посмотрите историю слоев и их ID:
```bash
docker history -q --no-trunc full-app
```
    Команда выведет список ID слоев от последнего к первому.
- Создайте тег/образ для конкретного слоя по его ID:
```bash
docker tag <LAYER_ID> layer-image:v1
```
## Способ 3. Программный разбор слоев из архива (экспорт слоев)
Образ Docker представляет собой tar-архив с директориями слоев. Вы можете экспортировать слои и упаковать их обратно:
- Сохраните образ в архив:
```bash
docker save -o full-app.tar full-app
```
- Распакуйте архив:
```bash
mkdir extracted && tar -xf full-app.tar -C extracted
```
- Внутри папки `extracted` вы увидите файл `manifest.json`, который ссылается на структуру слоев (`layers`), лежащих в виде отдельных папок с `layer.tar`.
- Каждую такую папку с `layer.tar` можно импортировать обратно в Docker как отдельный образ через `docker import`:
```bash
docker import extracted/<layer_id>/layer.tar custom-layer-image:latest
```