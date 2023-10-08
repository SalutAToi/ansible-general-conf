# TODO

- install ripgrep
- set neovim or lunarvim -or distro- as default editor
  - ubuntu : https://stackoverflow.com/questions/71741860/how-to-configure-neovim-with-update-alternatives-for-ex-view-and-vimdiff-behavio
- set a symlink or alternative (update-alt in ubuntu) for vim to lvim (or nvim)
- install :
  - nvim
  - lunarvim (?) : make sure deps are satisfied BEFORE install ! python install with package manager (npm if possible)
    - lunarvim is a distro on top of nvim, so nvim must be installed and lunarvim install happens at user level
  - tmux
    - tmux plugin manager : https://github.com/tmux-plugins/tpm (install via git on xdg data home)
  - zsh
  - fd (fdfind)
- intall nerdfonts
  - https://www.nerdfonts.com/font-downloads download and put in .local/share/fonts
    - dl ubuntu, hack, meslo
- configure zsh
  - install zsh-antigen (on debian, may require git on fedora). CACHE AND DATA FOLDER MUST EXIST (xdg, create them)
- consider installing bat and aliasing cat (cat with highligthing and line numbers)
- consider installing lsd or exa and aliasing ls (ls with colors, better features, ...)
- import dotfiles (do first !)
  - set config for local config repo (dotfiles) to status.showUntrackedFiles no
- consider making light/dark theme switch possible with term tools : https://shapeshed.com/vim-tmux-alacritty-theme-switcher/
- look at dotfiles for inspiration : https://github.com/shapeshed/dotfiles/ - modify dotfiles, not roles
## neovim (to be considered without lunarvim -maybe with ?-)

install latest neovim https://github.com/neovim/neovim/wiki/Installing-Neovim#install-from-package

- install plugin manager
- install tree-sitter (package) and treesitter neovim (plugin) : https://github.com/nvim-treesitter/nvim-treesitter
    - install (TSInstall) common languages
