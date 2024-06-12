# TODO

- verify all package names for fedora repos (and if needed for deps)
  
## Packages outside repo - Source info

### Teamviewer

#### Ubuntu

<https://community.teamviewer.com/English/kb/articles/45-install-teamviewer-classic-on-ubuntu>

## from term tools

# TODO

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

## neovim (to be considered without lunarvim -maybe with ?-)

install latest neovim <https://github.com/neovim/neovim/wiki/Installing-Neovim#install-from-package>

- install plugin manager
- install tree-sitter (package) and treesitter neovim (plugin) : <https://github.com/nvim-treesitter/nvim-treesitter>
  - install (TSInstall) common languages
