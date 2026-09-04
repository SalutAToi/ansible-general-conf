# workstation_common

Common workstation configuration: creates the two user profiles, sets editor
alternatives, applies system configuration, exposes the XDG variables and deploys SSH
public keys.

## Supported distributions

Fedora (GNOME) and Pop!_OS (COSMIC). Distribution differences are resolved from
`ansible_os_family`.

## Requirements

- Collection `community.general` (for `alternatives`)
- `zsh` installed before the role runs, because it is set as the login shell.
  `workstation_packages` installs it, so run that role first.

## Role variables

| Variable | Default | Description |
| --- | --- | --- |
| `workstation_common_scope` | `system` | `system`, `user` or `all`. Selects which half of the role runs. |
| `user_environment` | `perso` | `perso` or `work`. Set by the play, gates work-only tasks. |
| `user_shell` | `/usr/bin/zsh` | Login shell applied to every managed account. |
| `workstation_admin_group` | `wheel` on RedHat, `sudo` on Debian | Administrative group the accounts are added to. |
| `workstation_users` | `christophe_perso`, `christophe_work` | Accounts to create, with comment and groups. |
| `common_pub_ssh_keys` | `[id_ed25519.pub]` | Public keys deployed to every profile. |
| `work_pub_ssh_keys` | `[id_rsa_work.pub]` | Public keys deployed only when `user_environment` is `work`. |
| `alternatives` | editor and vim to nvim | Alternatives registered system-wide. |
| `user_services` | ssh-agent, gnome-keyring-daemon | User scope systemd services to enable. |
| `xdg_vars` | see `defaults/main.yml` | XDG variables written to `/etc/profile.d` and the directories created per user. |

Internal variables live in `vars/main.yml`.

## Dependencies

None declared in `meta/main.yml`.

## Scopes

The role is split so it can run once as root and once per profile:

- `system` — alternatives, account creation, hostname and ZDOTDIR, XDG profile file.
- `user` — XDG directories, user services, SSH public keys.

## Example usage

```yaml
- name: Configuration système du poste
  hosts: malatesta
  roles:
    - role: workstation_common
      workstation_common_scope: system

- name: Configuration du profil professionnel
  hosts: malatesta
  become: true
  become_user: christophe_work
  vars:
    user_environment: work
  roles:
    - role: workstation_common
      workstation_common_scope: user
```

## Notes

- Account passwords are deliberately not managed, so a run cannot lock an account out.
  Set them by hand on first provisioning.
- Only public keys are shipped. `id_ed25519.pub` must be added to `files/` before the
  role can deploy it.
