# TODO

Backlog relocated out of `README.md` and the per-role `README.md` files during the
repository refactor. Items are kept verbatim; none of them are actioned by the refactor
itself unless explicitly stated.

## Repository backlog (moved from `README.md`)

### Plugins and collections

- integrate the google-cloud collection and IAP plugin to reach servers

### Role creation

#### General

- hardening for workstations
  - research hardening guidelines for linux workstations
- installing ansible collections (for google, for example)
- separate users for work and personal
- add rclone and google drive mounting to work profile
- create a group for access to taskwarrior files that should be synced with nextcloud and create the folder
- add wl-clipboard
- in the program config, add a step to configure nextcloud by ensuring nextcloud.cfg doesn't exist, then symlinking it to a cfg for a work or personal profile

#### Fedora Sway based

- firmware upgrade management
  - install firmware upgrade tools
  - set up watcher for updates

#### PopOS Gnome based

#### Debian servers

- include qwerty-fr deb package : <https://github.com/qwerty-fr/qwerty-fr>

#### Redhat servers

- include qwerty-fr rpm package : <https://github.com/leopoldhub/qwerty-fr-rpm/releases/tag/v0.7.3>

## Role backlogs (moved from per-role `README.md` files)

### `program_config`

#### Fedora

##### Sway configuration

- remapping CAPS LOCK <-> Escape
- shortcuts
- screens
  - hidpi ?
  - look into kanshi

##### waybar configuration

##### rofi configuration

##### dunst configuration

##### swaylock configuration

#### Ubuntu

- Install nerd fonts

#### All

##### Firefox

- install multi profiles
- install shortcuts/launcher entry for multi profiles
- install extensions
- connect firefox account on priv profile
- tridactyl : bind / to good search mode <https://github.com/tridactyl/tridactyl/issues/64>
  - also, set editorcmd to "alacritty -e vim %f" to use ctrl i to open redaction page in vim

### `workstation_packages` (formerly `programs`)

- verify all package names for fedora repos (and if needed for deps)

#### Packages outside repo - Source info

##### Teamviewer

###### Ubuntu

<https://community.teamviewer.com/English/kb/articles/45-install-teamviewer-classic-on-ubuntu>

#### from term tools

- set neovim or lunarvim -or distro- as default editor
  - ubuntu : <https://stackoverflow.com/questions/71741860/how-to-configure-neovim-with-update-alternatives-for-ex-view-and-vimdiff-behavio>
- set a symlink or alternative (update-alt in ubuntu) for vim to lvim (or nvim)
- install :
  - nvim
  - lunarvim (?) : make sure deps are satisfied BEFORE install ! python install with package manager (npm if possible)
    - lunarvim is a distro on top of nvim, so nvim must be installed and lunarvim install happens at user level
- intall nerdfonts
  - <https://www.nerdfonts.com/font-downloads> download and put in .local/share/fonts
    - dl ubuntu, hack, meslo
- consider installing bat and aliasing cat (cat with highligthing and line numbers)
- consider installing exa and aliasing ls (ls with colors, better features, ...)
- consider making light/dark theme switch possible with term tools : <https://shapeshed.com/vim-tmux-alacritty-theme-switcher/>

#### neovim (to be considered without lunarvim -maybe with ?-)

install latest neovim <https://github.com/neovim/neovim/wiki/Installing-Neovim#install-from-package>

- install plugin manager
- install tree-sitter (package) and treesitter neovim (plugin) : <https://github.com/nvim-treesitter/nvim-treesitter>
  - install (TSInstall) common languages

### `work_profile_tools` (formerly `work_tools`)

- on fedora, make sure vagrant is installed from the fedora repo and not hashicorp as it creates issues with the vagrant-libvirt package
- install ansible and plugins with galaxy (aruba, other ?)
- replace pip by pipx for all tools (use inject for ansible and others), make sure all system level python packages are managed by the distro package manager insteead of pip
- install node latest <https://github.com/nodesource/distributions/blob/master/README.md> (needed for coc ?)
- ajouter les depots vagrant hashicorp (voir site) et ajouter plugin libvirt (pour install, voir site dédié)
- install terraform (from hashicorp) <https://developer.hashicorp.com/terraform/tutorials/gcp-get-started/install-cli>
- add vscode profiles from exported files
- add gpg signing to git config
- install lpass (detail somewhere else)
- install black (python)
- install nodejs from module (rpm), package (specify ver ! ) on ubu
  - node_version should be set (defaults or vars ? has to be checked or changed often because of time)
- github cli rpm : add community repos for fedora !

#### ansible

pour ansible, mettre dans le ansible.cfg, pour changer l'affichage des retours en yaml
[default]
stdout_callback = yaml

- pas dans le général, dans le user. ou ?

#### neovim

vim : rajout du support folding pour les langages majeurs
install coc-vim for python language, install plugin for coc-vim python (search name) - see other languages, compare with vsc !

not sure vim plugin manager is installed on anisble (?)
not sure the explainshell docker container is there

### `server_tools_machine` and `server_tools_user`

Note most "configure" stuff is done via the dotfiles

- install and configure tmux and use vim keybindings
- configure bash
- find list of utilities to install (optional step as need root !)
