
## Operating System
- **Distribution:** Ubuntu 22.04.5 LTS (Jammy Jellyfish)
- **Base:** Debian/Ubuntu
- **Architecture:** x86_64
- **Kernel:** Linux 5.15.0-179-generic
- **Desktop session:** X11
- **Hardware observed at Codam:** Intel Core i5-7500, 4 cores, 15 GiB RAM, 4 GiB swap
## Shell
- **Login shell:** Zsh 5.8.1 (`/bin/zsh`)
- **Interactive terminal shell:** Fish 3.3.1 (`/usr/bin/fish`)
- **Bash:** Available (`/usr/bin/bash`)
- **Terminal emulator:** GNOME Terminal 3.44.0
- **Terminal engine:** VTE 0.68.0
- **Terminal type:** `xterm-256color`
- **Color support:** Truecolor (`COLORTERM=truecolor`)
### Fish configuration
The interactive Fish configuration defines three custom functions:
- `mkvenv`: Creates a `.venv` using the Python interpreter selected by `pyenv`, then activates it.
- `workon`: Activates the `.venv` in the current directory.
- `uppip`: Upgrades pip in the active environment.
The Fish greeting is disabled.
## Development Toolchain

### C / C++
- **GCC:** 10.5.0
- **Clang:** 12.0.1
- **GNU Make:** 4.3
- **CMake:** Installed
- **GDB:** Installed
- **Valgrind:** Installed
GCC and Clang are available under `/usr/bin`.
### Python
- **pyenv:** Installed under `/opt/pyenv`
- **Python 3.14.1:** Installed through pyenv
- **pyenv global version:** `system`
- **Active Python in the reported environment:** 3.13.1
- **pip:** 24.3.1
- **Virtual environments:** Python `venv`, with a dedicated Flake8 environment
The PATH includes pyenv, pyenv-virtualenv shims and the Flake8 virtual environment.
### Other development tools
- **Node.js:** Installed
- **npm:** Installed
- **Rust:** `rustc` and `cargo` installed
- **Docker:** Installed
- **.NET SDK:** Version 8 installed
## Version Control
- **Git:** 2.34.1
- **Default initial branch:** `main`
- **Configuration file:** `~/.gitconfig`
Git is available at `/usr/bin/git`.
## Editor

### Neovim
- **Version:** 0.13.0-dev
- **Build type:** RelWithDebInfo
- **Lua runtime:** LuaJIT 2.1.0-beta3
- **Executable:** `/usr/bin/nvim`
- **Configuration directory:** `~/.config/nvim`
- **Plugin manager:** lazy.nvim
- **Plugin lock file:** `lazy-lock.json`
### Configuration structure
The configuration is modular and Lua-based.
- `init.lua` — entry point
- `lua/config/options.lua` — editor options
- `lua/config/keybinds.lua` — keybindings
- `lua/config/lazy.lua` — plugin manager setup
- `lua/plugins/` — individual plugin configurations
The plugin configuration files include:
- Autopairs
- Completion (`cmp`)
- Codam headers for C and Python
- Colors
- Dead column
- Harpoon
- Linting
- LSP
- Mason
- Nvim Tree
- Smear Cursor
- Telescope
- ToggleTerm
- Treesitter
Additional filetype-specific configurations exist under `after/ftplugin/` for C, Lua, Python and Makefiles.
The report identifies the configuration structure but does not include the actual contents of these files.
## Package Management
The system has the following package management tools:
- **APT:** `/usr/bin/apt`
- **DPKG:** `/usr/bin/dpkg`
- **Snap:** `/usr/bin/snap`
- **Flatpak:** `/usr/bin/flatpak`
APT/DPKG are the primary Ubuntu package management tools. Snap and Flatpak are also available.
Notable installed packages and software include:
- Multiple GCC and G++ versions (10, 11 and 12)
- Docker CE, Docker Buildx and Docker Compose
- .NET SDK 8
- GNOME desktop components
- Codam-specific packages:
    - `deepthought-worker` 3.1.4
    - `examshell` 3.3
The complete installed package list is much larger and includes system dependencies, desktop applications and hardware-related packages. It should not be copied wholesale when reproducing the environment.
## File System
The available report does not contain a complete disk, partition or filesystem inventory. Filesystem type, partition layout and mount configuration are therefore unknown.
Observed directory conventions:
- **User home:** `/home/<username>`
- **User configuration:** `~/.config`
- **Neovim configuration:** `~/.config/nvim`
- **Fish configuration:** `~/.config/fish`
- **pyenv installation:** `/opt/pyenv`
- **User executables:** `~/bin`
- **Python virtual environments:** `~/.venvs/flake8` and project-local `.venv` directories
The configuration directory contains application settings for GNOME, Fish, Neovim, browsers, Docker, Obsidian and other desktop applications.
## Desktop Environment
- **Desktop:** Ubuntu GNOME
- **GNOME Shell:** 42.9
- **Display server:** X11
- **GTK theme:** Adwaita-dark
- **Window theme:** Adwaita
- **Icon theme:** Win10Sur
- **Cursor theme:** Adwaita
- **Interface font:** Cantarell 11
- **Monospace font:** Monospace 11
- **Color scheme:** prefer-dark
- **Terminal:** GNOME Terminal 3.44.0
### GNOME extensions reported as installed
- App Menu
- Auto Move Windows
- Drive Menu
- Launch New Instance
- Native Window Placement
- Places Menu
- Screenshot Window Sizer
- User Themes
- Window List
- Windows Navigator
- Workspace Indicator
- Ubuntu Dock
- Dash to Dock
- Desktop Icons Neo
- Lock Screen (42 Network)
- Display Brightness (DDCUtil)
- Home Size
The extension listing confirms that these extensions are installed, but does not establish which are enabled.
### Backgrounds
- **Light/default background:** `file:///usr/share/42/42.png`
- **Dark background:** A user-selected image stored under `~/.local/share/backgrounds/`

### Desktop reproduction notes

The closest desktop match would use GNOME 42, X11, Adwaita Dark, the Win10Sur icon theme, Cantarell fonts and the listed GNOME extensions. Exact replication will require checking extension enablement, GNOME settings and the original background image.