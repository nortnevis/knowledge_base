```table-of-contents
```
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
### Сборка
Скачать [форк](https://github.com/nortnevis/clang-p2996).
```bash
cmake -B build -G "Ninja" \
	-DCMAKE_BUILD_TYPE=Release \
	-DCMAKE_INSTALL_PREFIX="install" \
	-DLLVM_TARGETS_TO_BUILD="X86" \
	-DCLANGD_BUILD_XPC=OFF \
	-DLLVM_ENABLE_PROJECTS="clang;clang-tools-extra" \
	-DLLVM_ENABLE_RUNTIMES="libcxx;libcxxabi;libunwind" \
	-DLIBCXX_HARDENING_MODE=extensive \
	-DLLVM_ENABLE_ASSERTIONS=OFF \
	-DLLVM_INCLUDE_TESTS=OFF \
	-DLLVM_INCLUDE_BENCHMARKS=OFF \
	llvm
	
cmake --build build -j16
```
### Установка
```bash
cmake --install build
```
Нас интересуют следующие директории:
- `install/bin/`, где содержатся clangd и прочие бинарники;
- `install/include/c++/v1/`, где содержатся заголовки STL библиотеки (`libcxx`).
- `install/lib/clang/21/include`, где находятся заголовки стандартной библиотеки C (`clangd` сам найдёт их относительно своей директории).
- `install/include/x86_64-unknown-linux-gnu/c++/v1/__config_site`, который следует скопировать в `install/include/c++/v1`.
В `.clangd` необходимо указать путь:
```yaml
CompileFlags:
  Add:
    - -std=c++26
    - -freflection-latest
    - -nostdinc++
    - -isystem
    - <path/to/libcxx/include>  # внутри неё должен быть `c++/v1`
```
В `.clangd` работают символические ссылки относительно директории, которая содержит `compile_commands.json`. Можно создать символическую ссылку до `install/include` в этой директории.
### Запуск в neovim
```lua
-- .nvim.lua

local project_root = vim.fs.root(0, { ".git", ".clangd" })
if project_root == "" then
	project_root = vim.fn.expand("<sfile>:p:h")
	if project_root == "" then
		project_root = vim.fn.getcwd()
	end
end

local clangd_path = project_root .. "/tools/bin/clangd"

vim.lsp.config("clangd", {
	cmd = {
		clangd_path,
		"--background-index",
		"--clang-tidy",
	},
	root_dir = project_root,
})

vim.lsp.enable("clangd")
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
---
# Отключите энергосбережение (Autosuspend) для Bluetooth
Очень часто контроллеры (особенно от Intel и Realtek) падают в ошибку `0x0c`, когда ядро пытается перевести их в режим сна или ограничить питание во время обмена данными с BLE-устройствами.
Вы можете временно отключить авто-сон для проверки, выполнив команду в терминале:
```bash
echo -1 | sudo tee /sys/bus/usb/devices/usb*/power/autosuspend
```
Используйте код с осторожностью.
_Если это помогло, зафиксируйте настройку навсегда._ Для этого добавьте параметр ядра `usbcore.autosuspend=-1` в конфигурацию вашего загрузчика (например, в `/etc/default/grub` в строку `GRUB_CMDLINE_LINUX_DEFAULT`, после чего обновите GRUB командой `sudo update-grub`).

---
# X11 Forwarding to Host GPU
Option 4: X11 Forwarding (Linux Host to Linux Guest)
If you just want to run a single Linux app inside the VM but have it render seamlessly on your host desktop:
1. On your **host** terminal, allow connections (for testing):
```bash
xhost +local:
```
2. Connect to your VM via SSH using the `-X` or `-Y` flag:
```bash
ssh -X user@vm_ip_address
```
3. Run your graphical application from that terminal (e.g., `firefox` or `vlc`). The window will pop up on your host desktop, using your host's display server.