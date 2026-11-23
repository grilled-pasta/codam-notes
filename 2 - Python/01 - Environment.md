- **pyenv** - Install and switch between Python versions
- **venv** - Create isolated environments for individual projects
- **pip** - Install Python packages
## Python

`python` - Start interactive Python interpreter (REPL)

Execute Python code and manage scripts through the terminal
### Commands
| Command               | Purpose                                       |
| --------------------- | --------------------------------------------- |
| `file.py`             | Execute a script                              |
| `-c "print('Hello')"` | Execute inline code                           |
| `-i file.py`          | Execute a script, then enter interactive mode |
| `-m MODULE`           | Execute a module as a script                  |
| `-m package.module`   | Execute a module inside a package             |
| `--version`           | Display Python version                        |
### CLI
| Command        | Purpose                 |
| -------------- | ----------------------- |
| `help()`       | Interactive help        |
| `help(str)`    | Documentation for `str` |
| `dir(object)`  | List object attributes  |
| `type(object)` | Inspect object type     |
| `id(object)`   | Inspect object identity |
| `exit()`       | Exit interpreter        |
### Debugging and Inspection
| Command                 | Purpose                   |
| ----------------------- | ------------------------- |
| `pdb file.py`           | Run script with debugger  |
| `compileall .`          | Compile files to bytecode |
| `dis file.py`           | Disassemble bytecode      |
| `timeit "expression"`   | Measure execution time    |
| `trace --trace file.py` | Trace program execution   |
## pyenv
Manage multiple Python installations without replacing the operating system's Python
```
Linux Mint
│
├── /usr/bin/python3
│   └── Python 3.10.12 (system)
│
└── ~/.pyenv/
    ├── versions/
    │   ├── 3.10.22/
    │   ├── 3.14.x/
    │   └── ...
    │
    └── shims/
        ├── python
        ├── python3
        └── pip
```
`shims` - small executable proxies that select the correct Python version based on the config
### Commands
| Command           | Purpose                               | Notes                            |
| ----------------- | ------------------------------------- | -------------------------------- |
| `versions`        | List installed version                |                                  |
| `install --list`  | List available versions               |                                  |
| `install 3.x.y`   | Install a version                     |                                  |
| `uninstall 3.x.y` | Remove a version                      |                                  |
| `global 3.x.y`    | Set default version                   |                                  |
| `local 3.x.y`     | Set version for the current directory | creates a `.python-version` file |
| `shell 3.x.y`     | Temporarily select version            | for the current shell session    |
| `version`         | Show active version                   |                                  |
| `which python`    | Show actual interpreter path          |                                  |
| `version`         | Show active Python version            |                                  |
| `rehash`          | Refresh current shims                 |                                  |
## venv

`python -m venv .venv`

Create an isolated package environment
```
Python 3.14.2
│
├── Environment A
│   ├── pytest 9.x
│   └── requests
│
├── Environment B
│   ├── pytest 8.x
│   └── numpy
│
└── Environment C
    └── ipython
```
Installing a package in *Environment A* does not install it in *Environment B*
### Workflow
```fish
mkdir my_project
cd my_project

python -m venv .venv

source .venv/bin/activate.fish        # activate it
python -m pip install pytest requests # install packages
python -m pip list                    # list packages
python main.py                        # run it
deactivate                            # deactivate it
```
## pip

`python -m pip`

Install packages in a Python environment
### Commands
| Command                       | Purpose                       |
| ----------------------------- | ----------------------------- |
| `install PACKAGE`             | Install a package             |
| `install PACKAGE==x.y.z`      | Install a specific version    |
| `install --upgrade PACKAGE`   | Upgrade a package             |
| `uninstall PACKAGE`           | Remove a package              |
| `freeze > requirements.txt`   | Export dependencies           |
| `install -r requirements.txt` | Install exported dependencies |
| `check`                       | Check dependencies            |
| `show PACKAGE`                | Inspect installed package     |
| `list`                        | List installed packages       |

> Use `python -m pip` instead of `pip` to explicitly associate pip with the current interpreter
## Nvim

### pyenv

```
mkdir -p ~/.venvs

python -m venv ~/.venvs/nvim-tools

~/.venvs/nvim-tools/bin/pip install --upgrade pip
~/.venvs/nvim-tools/bin/pip install basedpyright
~/.venvs/nvim-tools/bin/pip install flake8 mypy

fish_add_path ~/.venvs/nvim-tools/bin
```
### Shortcuts

~/.config/nvim/after/ftplugin/python.lua 
```
vim.keymap.set("n", "<leader>pr", function()
    vim.cmd("write")
    vim.cmd("belowright split | terminal python " .. vim.fn.shellescape(vim.fn.expand("%:p")))
end, { buffer = true, desc = "Run Python file" })

vim.keymap.set("n", "<leader>pi", function()
    vim.cmd("write")
    vim.cmd("belowright split | terminal python -i " .. vim.fn.shellescape(vim.fn.expand("%:p")))
end, { buffer = true, desc = "Run Python file interactively" })
```
### Linting

~/.config/nvim/lua/plugins/lint.lua 
```
return {
	"mfussenegger/nvim-lint",

	config = function()
		local lint = require("lint")

		lint.linters_by_ft = {
			python = { "flake8", "mypy" }
		}

		vim.api.nvim_create_autocmd("BufWritePost", {
			callback = function()
				lint.try_lint()
			end,
		})

		vim.api.nvim_create_user_command("Lint", function()
			lint.try_lint()
		end, {})
	end,
}
```
## Fish Shortcuts

```
# Create a Python virtual environment
function mkvenv
    python -m venv .venv
    source .venv/bin/activate.fish
end

# Activate existing virtual environment
function workon
    source .venv/bin/activate.fish
end

# Upgrade pip in current environment
function uppip
    python -m pip install --upgrade pip
end

# Reload
source ~/.config/fish/config.fish
```
## .gitignore

```
# env
.venv/
__pycache__
```
## flake8

`flake8 file.py` 
### .flake8

```
[flake8]
exclude= .venv,__pycache__,.mypy_cache
max-line-length = 80
```
## mypy

`mypy --strict file.py`
