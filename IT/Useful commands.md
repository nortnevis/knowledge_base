[[Start Page|На главную]]
```table-of-contents
```

---
# CURL
Загрузка осуществляется с помощью ключа `-L` для следования редиректам и `-o` для сохранения с кастомным именем (`-O` для сохранения с оригинальным именем).
```bash
curl -LSf -O "https://example.com"
```
- `-L` (location): автоматически перенаправляет, если ссылка изменилась.
- `-s` (silent): отключает отображение процесса загрузки (индикатор прогресса).
- `-S` (show-error): заставляет `curl` выводить ошибку, если запрос завершился неудачно.
- `-f` (fail): завершает работу `curl` с кодом ошибки без вывода страницы, если сервер вернул ошибку 40x или 50x.
# Git directory scope configs
Create a file (for example `.gitconfig-repos`) and write configs:
```ini
[user]
	name = <Name>
	email = <Email>

[core]
	autocrlf = false
	eol = lf
```
Then you can include this config in **global configs** file (for example `.gitconfig-repos` instead of `<config_path>`):
```ini
[includeIf "gitdir/i:<dir_path>/"]
        path = <config_path>
```
You must add `/` after `<dir_path>`.
# Git global config final example
```ini
[user]
        name = <Name>
        email = <Email>
[alias]
        comr = !sh -c 'git fetch $1 merge-requests/$2/head:mr-$1-$2 && git checkout mr-$1-$2' -
        co = checkout
        br = branch
        ci = commit
        st = status
        hist = log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --branches
[includeIf "gitdir/i:<dir_path>/"]
        path = <config_path>
```

---
lsof (List Open Files) — отображает информацию об открытых файлах, сетевых соединениях, сокетах и процессах, которые их используют.

```bash
lsof -p <PID>
```

Просмотр сетевых адаптеров Windows:
```run
ncpa.cpl
```

Быстро создать файл [StackOverflow](https://stackoverflow.com/questions/982659/quickly-create-large-file-on-a-windows-system):
```cmd
fsutil file createnew <filename> <length>
```

msvcmon parameters
```
msvcmon.exe /installed /nofirewallwarn /timeout 86400 /noauth /anyuser /nosecuritywarn
```

Проброс туннеля на примере отладки с `msvcmon`
```
ssh -nN -L localhost:4026:remotehost:4026 user@ssh-host
```

# System journal linux
Посмотреть используемое пространство:
```bash
journalctl --disk-usage
```
Задать лимит:
```bash
sudo journalctl --vacuum-size=500M
```

---
# PowerShell
## Git mv `*.cpp`
```powershell
git mv (Get-ChildItem *.cpp) ./src
```
## Formatting all files recursively
```
Get-ChildItem .\ProjectSourceFilesFolder -File -Recurse -Include "*.cpp","*.c","*.h","*.hpp" | Foreach {clang-format -i $_}
```

---
# Прочее
Путь до инструментов разработчика:
```
C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Auxiliary\Build
```

Путь до WDK (мб ещё и SDK)
```
C:\Program Files (x86)\Windows Kits\10\Include
```

Создать локальную ветку от удалённой и переключится на неё:
```git
git checkout -b branch1 origin/branch1
```

Использовать компилятор cl без создания проекта:
```cmd
cl /W4 /EHsc file1.cpp /link /out:program.exe
```

Конфиг для добавления профиля терминала в VS Code ${env:USERPROFILE}\AppData\Roaming\Code\User\setting.json
```json
"terminal.integrated.profiles.windows": {
	"Dev Command Prompt": {
		"path": [
			"${env:windir}\\Sysnative\\cmd.exe",
			"${env:windir}\\System32\\cmd.exe"
		],
		"args": [
			"/k",
			"C:\\Program Files\\Microsoft Visual Studio\\2022\\Community\\Common7\\Tools\\VsDevCmd.bat",
			"-arch=x64",
			"-host_arch=x64"
	],
		"icon": "terminal-cmd"
	},
}
```

Отобразить список переменных окружения в Powershell:
```powershell
gci env:* | Sort-Object name
```

Ещё один пример компиляции программы, только в batch-файле:
```bat
@echo off
del file.exe remove_entry.obj
cl /W4 /EHsc ../remove_entry.cpp /link /out:file.exe
echo:
echo:
.\file.exe
```

# Debian apt utility
Для отображения общей информации о пакете можно использовать команду:
`apt-cache show <package_name>=<package_version>`

Если нужно отобразить только зависимости (например у метапакетов), то можно использовать:
`apt-cache depends <package_name>=<package_version>`

Список обратных зависимостей (то есть список пакетов, которые зависят от `package_name`):
`apt-cache rdepends --installed <package_name>`
`--installed` ограничивает вывод теми пакетами, которые в данный момент установлены.

# Пигментация консоли
Для того, чтобы добавить цвета в `less` для xml можно использовать `pygmentize`. Примеры команд:
```bash
cat file.xml | pygmentize -l xml -f terminal | less -R
```
# WinDbg
WinDbg Server Creation
```
dbgsrv -t tcp:port=5005,password=1234 
```

WinDbg Server Connecting
```
tcp:server=sys-dev-017.avsw.local,port=5005,password=1234
```
