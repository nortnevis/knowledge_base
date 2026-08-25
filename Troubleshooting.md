# Boosting pacman speed (ArchLinux)
Сначала делаем бэкап `/etc/pacman.d/mirrorlist`. Затем используя пакет `reflector` запустить команду для изменения списка зеркал с сохранением в файл:
```bash
sudo reflector --verbose --country 'DE','NL','SE','FI' --latest 15 --sort rate --protocol https --save /etc/pacman.d/mirrorlist
```
# Change MTU
## Windows
See MTU values:
```powershell
netsh interface ipv4 show subinterfaces
```
Set mtu for specific interface:
```
netsh interface ipv4 set subinterface "<iface_name>" mtu=<value> store=persistent
```
# Traffic redirect
The `net.ipv4.ip_forward` parameter is a Linux kernel sysctl variable that controls whether your system routes network packets between different network interfaces.
Check:
```bash
sysctl net.ipv4.ip_forward
```
Change:
```bash
sysctl -w net.ipv4.ip_forward=1
```
# VS Code
## GDB Terminal error
Issue: warning: GDB: Failed to set controlling terminal: Operation not permitted
Solution: Add this to `settings.json`:
```json
"terminal.integrated.automationProfile.linux": {
	"path": "/bin/bash"
}
```
Or simply restart the system maybe?
# Obsidian
## Low fps
**Force GPU Usage via Windows Graphics Settings:**
- Open the Start menu, type **Graphics Settings**, and press Enter.
- Click **Browse** and locate your Obsidian executable (typically found at `C:\Users\YOUR_USERNAME\AppData\Local\Obsidian\Obsidian.exe`).
- Once added, click on Obsidian, select **Options**, and set it to use your **High Performance GPU** (NVIDIA or AMD).
# NeoVim
## C++26 clangd support
## План
1. Собрать bin файлы в форке.
2. Собрать `cmake --install build --prefix my_install`
3. Скопировать `clangd` и `clang++` в `tools/bin/` проекта
4. Скопировать `my_install/lib/clang/21/include/` в `tools/lib/clang/21/include/` проекта
5. Скопировать `libcxx/include` в `tools/lib/libcxx/include` проекта
6. Скопировать `__config_site` и `__assertion_handler` в `tools/libcxx/include проекта`
7. Добавить флаги в `.clangd`
8. Добавить параметры в `.nvim.lua` 
Нужно собрать проект: https://github.com/bloomberg/clang-p2996.git на ветке `p2996`. Запустить команду `cmake --install build --prefix <install_path>`.
Скопировать библиотеки и бинарники.
## Копирование библиотек
```bash
cp clang-p2996/libcxx/vendor/llvm/default_assertion_handler.in \
   <my_project>/tools/libcxx/include/__assertion_handler
```
Для `__config_site` команды копирования нет, потому что **готового файла не существует нигде** — его генерирует CMake при конфигурации libc++.
Два пути: сгенерировать, создать с нуля.
### Генерация:
```bash
mkdir build && cd build
cmake -G Ninja -DLLVM_ENABLE_RUNTIMES="libcxx;libcxxabi" ../runtimes
```
Итоговый путь: `build/include/c++/v1/__config_site` (or under a target-specific subdirectory like `build/include/x86_64-unknown-linux-gnu/c++/v1/__config_site`.
### Создание с нуля
Можно создать с нуля, вычислив значения из:
- шаблона `libcxx/include/__config_site.in` (что там `#cmakedefine`, `#cmakedefine01`),
- дефолтов `libcxx/CMakeLists.txt` (блок `config_define(...)` + опции: ABI=1, threads/monotonic/terminal/filesystem/random/localization/unicode/wide-chars/timezone=ON, PSTL=`std_thread`, hardening=`none`).
## Override clangd in `.nvim.lua`
Я скопировал `clangd` из папки `clang-p2996/build/bin/clangd` в папку `tools/` целевого проекта. 
```lua
-- .nvim.lua
local project_root = vim.fn.expand("<sfile>:p:h")
if project_root == "" then
	project_root = vim.fn.getcwd()
end

local clangd = project_root .. "/tools/bin/clangd"

vim.lsp.config("clangd", {
	cmd = {
		clangd,
		"--background-index --clang-tidy",
	},
	root_dir = vim.fs.root(0, { ".git", ".clangd", "compile_commands.json" }),
})

vim.lsp.enable("clangd")
```
Теперь надо настроить флаги компиляции и подключить собранные библиотеки. 
## Конфигурация `.clangd`
Начнём с `.clangd`:
```yaml
CompileFlags:
  CompilationDatabase: build/
  Add:
    - -std=c++26
    - -freflection-latest
    - -nostdinc++
    - -isystem
    - /path/to/project/root/tools/libcxx/include
```

## Autocompletion nvim-cmp в Windows terminal
Нужно вставить эти конфиги в `actions` или в `keybindings`:
```json
{
    "command": 
    {
        "action": "sendInput",
        "input": "\u001b[32;5u"
    },
    "keys": "ctrl+space"
}
```
У меня это было преобразовано в:
```json
{
	"command": 
	{
		"action": "sendInput",
		"input": "\u001b[32;5u"
	},
	"id": "User.sendInput.E116B028"
}
```