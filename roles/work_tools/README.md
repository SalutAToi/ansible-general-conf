
# TODO more up to date ?

- on fedora, make sure vagrant is installed from the fedora repo and not hashicorp as it creates issues with the vagrant-libvirt package
# TODO


- install ansible and plugins with galaxy (aruba, other ?)
- replace pip by pipx for all tools (use inject for ansible and others), make sure all system level python packages are managed by the distro package manager insteead of pip
- install node latest <https://github.com/nodesource/distributions/blob/master/README.md> (needed for coc ?)
- ajouter les depots vagrant hashicorp (voir site) et ajouter plugin libvirt (pour install, voir site dédié)
- install terraform (from hashicorp)
<https://developer.hashicorp.com/terraform/tutorials/gcp-get-started/install-cli>
- add vscode profiles from exported files
- add gpg signing to git config
- install lpass (detail somewhere else)
- install black (python)
- install nodejs from module (rpm), package (specify ver ! ) on ubu
  - node_version should be set (defaults or vars ? has to be checked or changed often because of time)
- github cli rpm : add community repos for fedora !

## ansible

pour ansible, mettre  dans le ansible.cfg, pour changer l'affichage des retours en yaml
[default]
stdout_callback = yaml

- pas dans le général, dans le user. ou ?

## neovim

vim : rajout du support folding pour les langages majeurs
install coc-vim for python language, install plugin for coc-vim python (search name) - see other languages, compare with vsc !

not sure vim plugin manager is installed on anisble (?)
not sure the explainshell docker container is there
