```bash
#!/usr/bin/env bash

echo "========================================"
echo " CODAM LINUX ENVIRONMENT REPORT"
echo "========================================"

section() {
    echo
    echo "========== $1 =========="
}

section "OPERATING SYSTEM"
cat /etc/os-release
uname -a
uname -m

section "DESKTOP ENVIRONMENT"
echo "Desktop: $XDG_CURRENT_DESKTOP"
echo "Session: $XDG_SESSION_TYPE"
echo "Display: $DISPLAY"
echo "Wayland: $WAYLAND_DISPLAY"

section "SHELL"
echo "Current shell: $SHELL"
echo "Process: $(ps -p $$ -o comm=)"
echo "User: $(whoami)"
echo "Home: $HOME"
echo "PATH: $PATH"

section "SHELL CONFIGURATION"
ls -la ~ | grep -E 'bash|zsh|fish|profile|config'
ls -la ~/.config 2>/dev/null

section "CPU AND MEMORY"
lscpu
free -h

section "DEVELOPMENT TOOLS"
for tool in \
    gcc cc clang make cmake gdb valgrind \
    git python3 python pip pip3 pyenv \
    nvim vim fish zsh bash \
    node npm rustc cargo docker
do
    if command -v "$tool" >/dev/null 2>&1; then
        printf "%-12s %s\n" "$tool" "$(command -v "$tool")"
    else
        printf "%-12s NOT FOUND\n" "$tool"
    fi
done

section "COMPILER VERSIONS"
gcc --version 2>/dev/null | head -1
clang --version 2>/dev/null | head -1
make --version 2>/dev/null | head -1

section "PYTHON"
python3 --version 2>/dev/null
python3 -m pip --version 2>/dev/null
pyenv versions 2>/dev/null

section "GIT"
git --version
git config --global --list 2>/dev/null

section "NEOVIM"
nvim --version 2>/dev/null | head -5

section "PACKAGE MANAGEMENT"
command -v apt
command -v snap
command -v flatpak
command -v dpkg

section "INSTALLED PACKAGES"
dpkg-query -W -f='${binary:Package}\t${Version}\n' 2>/dev/null

section "TERMINAL"
echo "TERM=$TERM"
echo "COLORTERM=$COLORTERM"
echo "Terminal process:"
ps -p "$(ps -p $$ -o ppid= | tr -d ' ')" -o comm= 2>/dev/null

section "CONFIGURATION DIRECTORIES"
find ~/.config -maxdepth 2 -type d 2>/dev/null | sort

section "END OF REPORT"
```

```bash
./codam-audit.sh > codam-report.txt 2>&1
```

```bash
#!/usr/bin/env bash

echo "===== GNOME ====="
gnome-shell --version
gnome-extensions list

echo "===== GNOME APPEARANCE ====="
for key in \
    gtk-theme \
    icon-theme \
    cursor-theme \
    font-name \
    monospace-font-name
do
    echo "$key:"
    gsettings get org.gnome.desktop.interface "$key"
done

echo "===== GNOME WINDOW THEME ====="
gsettings get org.gnome.desktop.wm.preferences theme

echo "===== GNOME SETTINGS ====="
gsettings get org.gnome.desktop.interface color-scheme
gsettings get org.gnome.desktop.background picture-uri
gsettings get org.gnome.desktop.background picture-uri-dark

echo "===== GNOME TERMINAL ====="
gnome-terminal --version

echo "===== FISH ====="
fish --version
find ~/.config/fish -maxdepth 2 -type f

echo "===== FISH CONFIGURATION ====="
cat ~/.config/fish/config.fish 2>/dev/null

echo "===== ZSH ====="
zsh --version
getent passwd "$USER" | cut -d: -f7

echo "===== PYENV ====="
pyenv versions
pyenv global

echo "===== COMPILER PATHS ====="
which -a gcc clang python3 nvim

echo "===== NEOVIM CONFIGURATION ====="
find ~/.config/nvim -maxdepth 3 -type f

```

```bash
./codam-audit2.sh > codam-report2.txt 2>&1
```

## GNOME extension states and versions
```bash
gnome-extensions list --enabled
gnome-extensions list --disabled
gnome-extensions list -d
```
## Development Tool Versions
```bash
gcc --version
g++ --version
clang --version
gdb --version
valgrind --version
cmake --version
python3 --version
pyenv versions
node --version
npm --version
rustc --version
cargo --version
docker --version
nvim --version
```

```text
gcc (Ubuntu 10.5.0-1ubuntu1~22.04.3) 10.5.0
Copyright (C) 2020 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

g++ (Ubuntu 10.5.0-1ubuntu1~22.04.3) 10.5.0
Copyright (C) 2020 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

Ubuntu clang version 12.0.1-19ubuntu3
Target: x86_64-pc-linux-gnu
Thread model: posix
InstalledDir: /usr/bin
GNU gdb (Ubuntu 12.1-0ubuntu1~22.04.2) 12.1
Copyright (C) 2022 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
valgrind-3.18.1
cmake version 3.22.1

CMake suite maintained and supported by Kitware (kitware.com/cmake).
Python 3.13.1
* system (set by /home/alkonsta/.pyenv/version)
  3.14.1
v24.11.1
11.6.2
rustc 1.75.0 (82e1608df 2023-12-21) (built from a source tarball)
cargo 1.75.0
Docker version 29.5.2, build 79eb04c
NVIM v0.13.0-dev
Build type: RelWithDebInfo
LuaJIT 2.1.0-beta3
Run "nvim -V1 -v" for more info
```