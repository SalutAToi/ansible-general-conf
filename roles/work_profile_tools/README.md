# work_profile_tools

Installs work tooling: container runtime, virtualization, infrastructure and cloud
CLIs at machine level, plus editor and Python tooling at user level.

## Supported distributions

Fedora (GNOME) and Pop!_OS (COSMIC). Vendor repositories are added per family and
keyed on distribution facts.

## Requirements

- Collection `community.general` (for `copr`)
- `group_vars` providing `package_arch` and `node_version` for Debian family hosts,
  and `rhel_base_version` for RedHat family hosts
- The `dotfiles` role, whose `reapply` tasks are reused after cloning tools

## Role variables

| Variable | Default | Description |
| --- | --- | --- |
| `work_profile_tools_scope` | `system` | `system`, `user` or `all`. Selects which half of the role runs. |
| `microsoft_repo_ubuntu_version` | `{{ ansible_distribution_version }}` | Ubuntu release used to build the Microsoft repository URL. Pop!_OS tracks Ubuntu versions. |
| `docker_apt_distro` | `ubuntu` | Distribution path used in the Docker apt repository URL. |
| `uv_install_script_url` | `https://astral.sh/uv/install.sh` | Vendor installer script used to install `uv` system-wide. |
| `uv_install_dir` | `/usr/local/bin` | Shared install location for the `uv` binary, on every account's `PATH` by default. |

Repository definitions, package lists, `git_tools` and `uv_tools` live in
`vars/main.yml`.

## Dependencies

None declared in `meta/main.yml`. The role calls `dotfiles` through `include_role`
with `tasks_from: reapply`, so that role must be present.

## Scopes

- `system` — vendor repositories and packages. Requires root, runs once per host.
- `user` — Python applications installed with uv and git-based editor tooling. Runs for both profiles.

## Example usage

```yaml
- role: work_profile_tools
  work_profile_tools_scope: user
```

## Notes

- Despite the role name, the user-level half runs for both profiles. The machine-level
  packages are shared by every account on the host anyway.
- Docker Compose is installed as `docker-compose-plugin` and invoked as
  `docker compose`. The standalone `docker-compose` package is deprecated.
- `uv` is not consistently packaged across supported distributions, so it is installed
  system-wide from the vendor's installer script (`uv_install_script_url`) rather than
  the distro package manager. Each user's own `uv tool install` environments still live
  under their own XDG directories.
