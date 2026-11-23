===== GNOME =====
GNOME Shell 42.9
apps-menu@gnome-shell-extensions.gcampax.github.com
auto-move-windows@gnome-shell-extensions.gcampax.github.com
drive-menu@gnome-shell-extensions.gcampax.github.com
launch-new-instance@gnome-shell-extensions.gcampax.github.com
native-window-placement@gnome-shell-extensions.gcampax.github.com
places-menu@gnome-shell-extensions.gcampax.github.com
screenshot-window-sizer@gnome-shell-extensions.gcampax.github.com
user-theme@gnome-shell-extensions.gcampax.github.com
window-list@gnome-shell-extensions.gcampax.github.com
windowsNavigator@gnome-shell-extensions.gcampax.github.com
workspace-indicator@gnome-shell-extensions.gcampax.github.com
ubuntu-dock@ubuntu.com
dash-to-dock@micxgx.gmail.com
desktopicons-neo@darkdemon
lockscreen@42network.org
display-brightness-ddcutil@themightydeity.github.com
homesize@42angouleme.fr
===== GNOME APPEARANCE =====
gtk-theme:
'Adwaita-dark'
icon-theme:
'Win10Sur'
cursor-theme:
'Adwaita'
font-name:
'Cantarell 11'
monospace-font-name:
'Monospace 11'
===== GNOME WINDOW THEME =====
'Adwaita'
===== GNOME SETTINGS =====
'prefer-dark'
'file:///usr/share/42/42.png'
'file:///home/alkonsta/.local/share/backgrounds/2026-10-09-22-27-56-conor-sexton-hRemch0ZDwI-unsplash.jpg'
===== GNOME TERMINAL =====
# GNOME Terminal 3.44.0 using VTE 0.68.0 +BIDI +GNUTLS +ICU +SYSTEMD
===== FISH =====
fish, version 3.3.1
/home/alkonsta/.config/fish/fish_variables
/home/alkonsta/.config/fish/config.fish
===== FISH CONFIGURATION =====
if status is-interactive
    # Commands to run in interactive sessions can go here
    set fish_greeting
    
    # Create a Python virtual environment
    function mkvenv
	if test -d .venv
	    echo "Virtual environment already exists."
	    return 1
	end
	
	set -l python_path (pyenv which python)

	$python_path -m venv .venv
	or return 1
	
	source .venv/bin/activate.fish

	python --version
    end

    # Activate existing virtual environment
    function workon
	source .venv/bin/activate.fish
    end

    # Upgrade pip in current environment
    function uppip
	python -m pip install --upgrade pip
    end
end
===== ZSH =====
zsh 5.8.1 (x86_64-ubuntu-linux-gnu)
/bin/zsh
===== PYENV =====
* system (set by /home/alkonsta/.pyenv/version)
  3.14.1
system
===== COMPILER PATHS =====
/usr/bin/gcc
/bin/gcc
/usr/bin/clang
/bin/clang
/home/alkonsta/.venvs/flake8/bin/python3
/opt/pyenv/shims/python3
/usr/bin/python3
/bin/python3
/usr/bin/nvim
/bin/nvim
===== NEOVIM CONFIGURATION =====
/home/alkonsta/.config/nvim/init.lua
/home/alkonsta/.config/nvim/lazy-lock.json
/home/alkonsta/.config/nvim/lua/config/keybinds.lua
/home/alkonsta/.config/nvim/lua/config/lazy.lua
/home/alkonsta/.config/nvim/lua/config/options.lua
/home/alkonsta/.config/nvim/lua/plugins/autopairs.lua
/home/alkonsta/.config/nvim/lua/plugins/cmp.lua
/home/alkonsta/.config/nvim/lua/plugins/codam-header-python.lua
/home/alkonsta/.config/nvim/lua/plugins/codam-header.lua
/home/alkonsta/.config/nvim/lua/plugins/colors.lua
/home/alkonsta/.config/nvim/lua/plugins/deadcolumn.lua
/home/alkonsta/.config/nvim/lua/plugins/disable.lua
/home/alkonsta/.config/nvim/lua/plugins/harpoon.lua
/home/alkonsta/.config/nvim/lua/plugins/lint.lua
/home/alkonsta/.config/nvim/lua/plugins/lsp.lua
/home/alkonsta/.config/nvim/lua/plugins/mason.lua
/home/alkonsta/.config/nvim/lua/plugins/nvim-tree.lua
/home/alkonsta/.config/nvim/lua/plugins/smear_cursor.lua
/home/alkonsta/.config/nvim/lua/plugins/telescope.lua
/home/alkonsta/.config/nvim/lua/plugins/toggleterm.lua
/home/alkonsta/.config/nvim/lua/plugins/treesitter.lua
/home/alkonsta/.config/nvim/.git/description
/home/alkonsta/.config/nvim/.git/packed-refs
/home/alkonsta/.config/nvim/.git/logs/HEAD
/home/alkonsta/.config/nvim/.git/HEAD
/home/alkonsta/.config/nvim/.git/config
/home/alkonsta/.config/nvim/.git/index
/home/alkonsta/.config/nvim/.git/info/exclude
/home/alkonsta/.config/nvim/.git/hooks/prepare-commit-msg.sample
/home/alkonsta/.config/nvim/.git/hooks/pre-merge-commit.sample
/home/alkonsta/.config/nvim/.git/hooks/update.sample
/home/alkonsta/.config/nvim/.git/hooks/pre-commit.sample
/home/alkonsta/.config/nvim/.git/hooks/pre-applypatch.sample
/home/alkonsta/.config/nvim/.git/hooks/pre-receive.sample
/home/alkonsta/.config/nvim/.git/hooks/fsmonitor-watchman.sample
/home/alkonsta/.config/nvim/.git/hooks/applypatch-msg.sample
/home/alkonsta/.config/nvim/.git/hooks/push-to-checkout.sample
/home/alkonsta/.config/nvim/.git/hooks/commit-msg.sample
/home/alkonsta/.config/nvim/.git/hooks/pre-rebase.sample
/home/alkonsta/.config/nvim/.git/hooks/post-update.sample
/home/alkonsta/.config/nvim/.git/hooks/pre-push.sample
/home/alkonsta/.config/nvim/.git/COMMIT_EDITMSG
/home/alkonsta/.config/nvim/after/ftplugin/Makefile.lua
/home/alkonsta/.config/nvim/after/ftplugin/c.lua
/home/alkonsta/.config/nvim/after/ftplugin/lua.lua
/home/alkonsta/.config/nvim/after/ftplugin/python.lua
