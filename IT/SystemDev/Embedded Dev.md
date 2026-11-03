```table-of-contents
```
---
# Yocto Project
## Основы
Выбираем слой:
. setup `<layer_name>`
## Вывод рабочего окружения
Чтобы вывести рабочее окружение для сборки по рецепту: `bitbake -e <recipe_name>`.
Как вывести build-директорию: `bitbake -e bmcweb | grep ^B=`
## DevTool
### Создание патчей
Используем devtool для модификации исходного кода по рецепту в выбранном слое: `devtool modify <recipe_name>`.
Собираем командой `bitbake <recipe_name>` (или `devtool build <recipe_name>`)
Можно отобразить вывод компилятора флагами: `bitbake -v -c compile <recipe_name>`
### Завершение работы
- **`devtool reset <recipename>`** — удаляет рецепт из вашей рабочей среды (`workspace`) и возвращает всё в исходное состояние. Исходный код в директории workspace при этом по умолчанию сохраняется.
- **`devtool build -c <recipename>`** (или очистка через `bitbake`) — чтобы выполнить очистку конкретных тасков (например, `bitbake -c clean <recipename>`), если нужно пересобрать пакет с нуля.
- **`devtool finish`** — завершает работу над рецептом, переносит изменения в ваш слой и автоматически удаляет рецепт из рабочей среды.
### Генерация compile_commands.json
```
bear -- devtool build <recipe>
```
### Генерация SDK
`devtool ide-sdk bmcweb obmc-phosphor-image --ide=none`
