```table-of-contents
```

---
# NVChad
## Пути до бинарников Mason
По умолчанию это `~/.local/share/nvim/mason/bin`.

---
# VS Code's `Ctrl+D` analogue in neovim
In Vim, native **`Ctrl+d` scrolls down half a screen**. If you are looking for an analogue to **VS Code/Sublime Text's `Ctrl+d`** (which selects the current word and creates multiple cursors to edit next occurrences), Vim achieves this using the **`cgn` operator** combined with the **dot (`.`) command**.
The Native Vim Workflow (`cgn` + `.`)
Vim does not use multiple cursors natively. Instead, it relies on a powerful search-and-replace mechanism:
1. Place your cursor on the word you want to change.
2. Press **`*`** (asterisk) to search for that word forward, or **`#`** to search backward.
3. Type **`cgn`** to **C**hange the **G**ewohlte (selected) **N**ext match. This deletes the word and puts you in _Insert Mode_.
4. Type your new text, then press **`Esc`** to return to _Normal Mode_.
5. Press **`.`** (dot) to repeat the exact same change on the next occurrence, or press **`n`** to skip it.
# tabulation
Ввести команду `:echo stdpath('config')`, которая покажет директорию для конфигураций. В данной директории в файле `init.lua` вводим следующие параметры:
```lua
vim.opt.tabstop = 4         -- A TAB character looks like 4 spaces
vim.opt.shiftwidth = 4      -- Number of spaces inserted when indenting
```
# Как загрузить vim plug
## UNIX (bash)
```bash
sh -c 'curl -fLo "${XDG_DATA_HOME:-$HOME/.local/share}"/nvim/site/autoload/plug.vim --create-dirs \
       https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim'
```
## Windows (PowerShell)
```powershell
iwr -useb https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim |`
    ni $HOME/vimfiles/autoload/plug.vim -Force
```
Он установить его в `~\vimfiles\autoload\plug.vim`
# Как установить плагины
В `nvim.lua` прописать:
```lua
local Plug = vim.fn['plug#']
vim.call('plug#begin')
-- Shorthand notation for GitHub:
Plug('<user_name>/<repo_name>')
Plug('https://github.com/<user_name>/<repo_name>.git')
vim.call('plug#end')
```
Затем нужно перезайти в `nvim` и выполнить команду: `:PlugInstall`
# Как поменять бинды
Назначим кнопку `<leader>` которая позволит активировать последовательность из уже занятых стандартных клавиш. По умолчанию это обратный слэш `\`.
Меняем её:
```lua
vim.g.mapleader = " " -- это пробел
```
Далее можно менять бинды и задействовать клавишу `<leader>`:
```lua
local map = vim.keymap.set
local opts = { noremap = true, silent = true }
-- noremap: prohibit recursive execution
-- silent: hide command display at bottom

-- <mode>, <keys>, <action or keys>
map('n', '<leader>n', ':NERDTreeFocus<CR>', opts)
map('n', '<C-n>', ':NERDTree<CR>', opts)
map('n', '<C-t>', ':NERDTreeToggle<CR>', opts)
map('n', '<C-f>', ':NERDTreeFind<CR>', opts)
```
# Colemak раскладка
```lua
local map = vim.keymap.set

map({'n', 'v', 'i'}, 'f', 'e')
map({'n', 'v'}, 'p', 'r')
map({'n', 'v'}, 'g', 't')
map({'n', 'v'}, 'j', 'y')
map({'n', 'v'}, 'l', 'u')
map({'n', 'v', 'i'}, 'f', 'e')
map({'n', 'v'}, 'p', 'r')
map({'n', 'v'}, 'g', 't')
map({'n', 'v'}, 'j', 'y')
map({'n', 'v'}, 'l', 'u')
map({'n', 'v'}, 'u', 'i')
map({'n', 'v'}, 'y', 'o')
map({'n', 'v'}, ':', 'p')
map({'n', 'v'}, 'r', 's')
map({'n', 'v'}, 's', 'd')
map({'n', 'v'}, 't', 'f')
map({'n', 'v'}, 'd', 'g')
map({'n', 'v'}, 'n', 'j')
map({'n', 'v'}, 'e', 'k')
map({'n', 'v'}, 'i', 'l')
map({'n', 'v'}, 'o', ':')
map({'n', 'v'}, 'k', 'n')

map({'n', 'v'}, 'F', 'E')
map({'n', 'v'}, 'P', 'R')
map({'n', 'v'}, 'G', 'T')
map({'n', 'v'}, 'J', 'Y')
map({'n', 'v'}, 'L', 'U')
map({'n', 'v'}, 'U', 'I')
map({'n', 'v'}, 'Y', 'O')
map({'n', 'v'}, ':', 'P')
map({'n', 'v'}, 'R', 'S')
map({'n', 'v'}, 'S', 'D')
map({'n', 'v'}, 'T', 'F')
map({'n', 'v'}, 'D', 'G')
map({'n', 'v'}, 'N', 'J')
map({'n', 'v'}, 'E', 'K')
map({'n', 'v'}, 'I', 'L')
map({'n', 'v'}, 'O', ':')
map({'n', 'v'}, 'K', 'N')
```
```