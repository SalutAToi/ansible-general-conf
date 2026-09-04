# work_profile_tools

Installs work tooling: container runtime, virtualization, infrastructure and cloud
CLIs at machine level, plus editor and Python tooling at user level.

## Supported distributions

Fedora (GNOME) and Pop!_OS (COSMIC). Vendor repositories are added per family and
keyed on distribution facts.

## Requirements

- Collection `community.general` (for `copr` and `pipx`)
- `group_vars` providing `package_arch` and `node_version` for Debian family hosts,
  and `rhel_base_version` for RedHat family hosts
- `program_config`, whose `reapply_dotfiles` tasks are reused after cloning tools

## Role variables

| Variable | Default | Description |
| --- | --- | --- |
| `work_profile_tools_scope` | `system` | `system`, `user` or `all`. Selects which half of the role runs. |
| `microsoft_repo_ubuntu_version` | `{{ ansible_distribution_version }}` | Ubuntu release used to build the Microsoft repository URL. Pop!_OS tracks Ubuntu versions. |
| `docker_apt_distro` | `ubuntu` | Distribution path used in the Docker apt repository URL. |

Repository definitions, package lists, `git_tools` and `pipx_tools` live in
`vars/main.yml`.

## Dependencies

None declared in `meta/main.yml`. The role calls `program_config` through
`include_role` with `tasks_from: reapply_dotfiles`, so that role must be present.

## Scopes

- `system` — vendor repositories and packages. Requires root, runs once per host.
- `user` — pipx applications and git-based editor tooling. Runs for both profiles.

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
