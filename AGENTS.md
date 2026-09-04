# Contributor and agent guide

Conventions for this repository. Follow them for every change so the codebase stays
internally consistent. This file is normative: if code and this guide disagree, the code
is wrong.

## Repository purpose

Two distinct concerns live here:

- **Workstation configuration** — fast, reliable redeployment of a personal workstation
  after a reinstall. Supported: Fedora (GNOME) and Pop!_OS (COSMIC).
- **Server tooling** — provisioning and configuration of servers. Every server role is
  prefixed `server_`. Supported: Ubuntu LTS, Debian, Rocky Linux (latest major, RHEL
  compatible).

A role without the `server_` prefix is a workstation role and must not be added to
`server-playbook.yml`.

## Distribution support

- **Never branch on inventory group membership** to decide what to install or configure.
  Branch on facts: `ansible_os_family`, `ansible_distribution`,
  `ansible_distribution_release`, `ansible_distribution_major_version`.
- Inventory groups exist to select *which hosts a play targets*, not to decide *what a
  task does*.
- Use `ansible_os_family` for family-wide behaviour (`Debian`, `RedHat`) and
  `ansible_distribution` only when package names or paths genuinely differ between
  distributions in the same family (for example Ubuntu vs Debian).
- Package name lists are keyed by distribution in `vars/main.yml`, and the task selects
  the right list from a fact. Verify names against the real repositories before adding
  them — see [Validation](#validation).

## Role structure

Every role has:

```
roles/<role>/
  README.md          # required, see Documentation
  meta/main.yml      # required, declares dependencies (empty list if none)
  defaults/main.yml  # user-overridable settings
  vars/main.yml      # internal constants and distro package maps
  tasks/main.yml     # entry point, dispatches to the files below
  tasks/*.yml        # focused task files
  handlers/main.yml  # only if handlers are needed
  files/             # only if static files are shipped
```

`tasks/main.yml` is a dispatcher. It includes other task files and applies `become` and
OS conditionals at the block level, so individual task files stay focused:

```yaml
- block:
    - include_tasks: "{{ item }}"
      loop:
        - rhel_based.yml
  become: true
  when: ansible_os_family == "RedHat"
```

## Variables

- **`defaults/main.yml`** — anything a user may reasonably want to override: package
  lists they might extend, paths, versions, feature toggles, user lists.
- **`vars/main.yml`** — internals that should not be overridden casually, and
  distribution-keyed lookup maps.
- **`group_vars/`** — host-group facts such as `rhel_base_version`, `node_version`,
  `package_arch`.
- **No hardcoded values in tasks.** Literal paths, URLs, versions, usernames and home
  directories belong in `defaults` or `vars`. A task should read as logic, not data.
- **Never hardcode a home directory or username.** Use `ansible_env.HOME`, or
  `ansible_env.XDG_CONFIG_HOME | default(ansible_env.HOME + '/.config')`. This repository
  provisions more than one user account, so `/home/<name>` literals are always a bug.

## Third-party software

Anything installed outside the distribution package manager must follow these rules.

### Package repositories

- Use the vendor's own documented repository URL and signing key for the target
  distribution and release. Never a random mirror, never a copied key file when the
  vendor publishes one over HTTPS.
- Debian family: `ansible.builtin.deb822_repository` with `signed_by`. Do not use
  `apt_key` or `apt-key add`; both are deprecated.
- RedHat family: `ansible.builtin.yum_repository` with `gpgkey`, or
  `community.general.copr` for COPR.
- Derive release-specific parts of a URL from facts or from a documented variable rather
  than hardcoding a release number.

### Downloads and clones

- Prefer resolving the latest version dynamically over pinning: query the GitHub releases
  API with `ansible.builtin.uri`, use a checksums manifest, or let `git` track the
  default branch.
- Guard dynamic resolution with an idempotency check so a normal run does not redownload
  or re-resolve every time.
- Install and configure each tool the way its own documentation recommends. A bare
  `git clone` is not an installation if the tool expects a build, a bootstrap command or
  a config file.

### Clones into dotfiles-managed paths

Dotfiles are a bare repository checked out over `$HOME`. Cloning a tool into a path the
dotfiles also track will conflict. Wherever this can happen, use this order:

1. Remove the target directory.
2. Clone the tool.
3. Re-apply the dotfiles checkout so tracked files are restored.

Use the shared task file for step 3 rather than repeating the `git --git-dir=...` command.

## Idempotency

- A second run must report no changes. Prefer modules over `command`/`shell`.
- When `command`/`shell` is unavoidable, set `creates`, `removes`, or a `when` guard
  backed by a `stat`/registered result, and set `changed_when` deliberately.
- Genuinely interactive steps (hardware key enrollment, TPM enrollment) cannot be made
  idempotent. Guard them so they are skipped once done, and document them in the role
  README as manual steps.

## Multi-user workstation profiles

The workstation provisions two real OS accounts with different configuration:

- `christophe_perso` — personal
- `christophe_work` — work

`user_environment` (`perso` or `work`) selects environment-specific behaviour. It is set
per play, not threaded through every task, so `become_user` and `ansible_env.HOME`
resolve naturally for the account being configured.

- System-wide roles run once, as root, in the system play.
- User-scoped roles run once per profile play.
- Gate environment-specific tasks with `when: user_environment == 'work'` (or `'perso'`).
- Tasks that apply to both profiles carry no `user_environment` condition at all.

## Secrets

- **Never commit a private key.** Only public keys belong in a role's `files/`.
- Secrets go in `vault.yml` and are referenced as variables.
- Do not print secrets in task output; set `no_log: true` where a task would.

## Documentation

Documentation is part of the change, not a follow-up. A change is incomplete without it.

When you change | Update
--- | ---
a variable in `defaults/` or `vars/` | the role README variables table
a role's behaviour or scope | the role README purpose section
a role's dependencies | `meta/main.yml` and the role README dependencies section
adding or removing a role | the role index in the top-level `README.md`
supported distributions | the role README and the top-level `README.md`
a manual or interactive step | the role README, explicitly called out

Every role README has: purpose, supported distributions, requirements and collections,
variables table with defaults, dependencies, example usage, and any manual steps.

Backlog items belong in `TODO.md`, not in role READMEs and not in the top-level README.

## Validation

Run before considering a change done:

```bash
ansible-lint .
ansible-playbook -i inventory.yml workstation_playbook.yml --syntax-check
ansible-playbook -i inventory.yml server-playbook.yml --syntax-check
ansible-inventory -i inventory.yml --graph
```

Then dry-run against the groups the change touches:

```bash
ansible-playbook -i inventory.yml server-playbook.yml --check --diff --limit debian
```

When adding or renaming a package, confirm it exists in the target repository first:

```bash
apt-cache policy <package>     # Debian, Ubuntu, Pop!_OS
dnf repoquery <package>        # Fedora, Rocky
```

Interactive steps cannot be validated in check mode. Verify them by hand and note them in
the role README.

## Style

- YAML starts with `---`, two-space indentation, no tabs.
- Every task has a descriptive `name`.
- Use fully qualified module names (`ansible.builtin.copy`, `community.general.pamd`).
- Use `true`/`false`, not `yes`/`no`.
- Comment only what the code cannot say itself — a workaround, a vendor quirk, a reason
  for a non-obvious conditional. Do not narrate what the next line does.
- Task names in this repository are historically French; keep a role internally
  consistent rather than mixing languages within one file.
