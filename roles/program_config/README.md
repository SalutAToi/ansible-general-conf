# TODO

- configure nextcloud
- configure alacritty
    - download fonts for it
- configure taskwarrior
- configure transmission to save to Vidéos
- webdav/caldav integration for nextcloud based on de
- add "ZDOTDIR=${XDG_CONFIG_HOME:-$HOME/.config}/zsh" to /etc/zsh/zshenv
## alacritty
add to the .config/alacritty/alacritty.yml file  :
env:
    TERM: xter-256color
font:
  normal:
    family: "UbuntuMono Nerd Font"
  size: 13.5

install font for alacritty/powerline10k: https://github.com/romkatv/powerlevel10k#meslo-nerd-font-patched-for-powerlevel10k (and p10k ?)
add font to alacritty config according to p10k install guide
meslo doesn't seem to work, try ubuntumono. also, modify the config file accordingly
## taskwarrior

taskwarrior  : transfer .local/share/task/ data and create a config file with data.location set to it
