# Сборка
```bash
docker build -t [HOST[:PORT_NUMBER]/]PATH[:TAG] <Dockerfile_path>
```
Собирается образ из `Dockerfile`, который ищется по пути `<Dockerfile_path>`.
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