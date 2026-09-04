# Ansible - Workstation and server configuration

Ansible roles and playbooks for two purposes:

- **Workstation configuration** — redeploying a personal workstation quickly and
  reliably after a reinstall, with two separate user profiles.
- **Server tooling** — provisioning and configuring servers. Server roles are prefixed
  `server_`.

## Requirements

- `ansible-core` 2.15 or newer
- Collection `community.general`
- `ansible-lint` for validation
- An Ansible Vault password for `vault.yml`, which holds `user_pwd` and `yubikey_pin`

```bash
ansible-galaxy collection install community.general
```

## Supported distributions

| Target | Distributions |
| --- | --- |
| Workstation | Fedora (GNOME), Pop!_OS (COSMIC) |
| Server | Ubuntu LTS, Debian, Rocky Linux / RHEL compatible |

Distribution-specific behaviour is always selected from Ansible facts
(`ansible_os_family`, `ansible_distribution`), never from inventory group membership.
Groups only decide which hosts a play targets.

## Roles

### Workstation

| Role | Purpose |
| --- | --- |
| [workstation_common](roles/workstation_common/README.md) | User accounts, alternatives, system config, XDG, SSH public keys |
| [workstation_packages](roles/workstation_packages/README.md) | Distribution packages, flatpaks, Nerd Fonts, git tools |
| [work_profile_tools](roles/work_profile_tools/README.md) | Container, virtualization, infrastructure and cloud tooling |
| [program_config](roles/program_config/README.md) | Dotfiles checkout and the shared dotfiles re-apply tasks |
| [sources](roles/sources/README.md) | Third-party package repositories and signing keys |
| [pam](roles/pam/README.md) | YubiKey authentication through `pam_u2f` |
| [luks_fido2](roles/luks_fido2/README.md) | FIDO2-backed LUKS unlock |
| [luks_tpm](roles/luks_tpm/README.md) | TPM-backed LUKS unlock |

### Server

| Role | Purpose |
| --- | --- |
| [server_tools_machine](roles/server_tools_machine/README.md) | Machine-level packages, alternatives, XDG, shell config |
| [server_tools_user](roles/server_tools_user/README.md) | Dotfiles, git tools and pipx applications |

## Usage

### Workstation

```bash
ansible-playbook -i inventory.yml workstation_playbook.yml --ask-vault-pass
```

The playbook runs three plays:

1. **System** — roles needing root, once per host.
2. **Personal profile** — user-scoped roles as `christophe_perso`, with
   `user_environment: perso`.
3. **Work profile** — the same user-scoped roles as `christophe_work`, with
   `user_environment: work`.

Roles that do both system and user work expose a `*_scope` variable so each play runs
only the relevant half.

### Server

```bash
ansible-playbook -i inventory.yml server-playbook.yml --ask-vault-pass
```

Targets the `centos`, `debian` and `ubuntu` inventory groups.

### Limiting a run

```bash
ansible-playbook -i inventory.yml server-playbook.yml --check --diff --limit debian
```

## User profiles

The workstation provisions two real OS accounts:

| Account | `user_environment` | Notes |
| --- | --- | --- |
| `christophe_perso` | `perso` | Personal profile |
| `christophe_work` | `work` | Work profile, gets the additional work SSH key |

Both profiles share the same dotfiles repository and branch, and both get a
`u2f_mappings` entry for YubiKey sudo. Account passwords are not managed by Ansible
and must be set by hand on first provisioning.

## Repository layout

```
inventory.yml              hosts and groups
workstation_playbook.yml   system play plus one play per user profile
server-playbook.yml        server play
vault.yml                  encrypted secrets
group_vars/                per-group variables
roles/                     roles, one directory each
AGENTS.md                  contributor conventions
TODO.md                    backlog
```

## Secrets

Only public keys are stored in this repository. Private keys are never committed.
Secrets belong in `vault.yml`.

## Validation

```bash
ansible-lint .
ansible-playbook -i inventory.yml workstation_playbook.yml --syntax-check
ansible-playbook -i inventory.yml server-playbook.yml --syntax-check
ansible-inventory -i inventory.yml --graph
```

Conventions for contributing, including when documentation must be updated, are in
[AGENTS.md](AGENTS.md).
