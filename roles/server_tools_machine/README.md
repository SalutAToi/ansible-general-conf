# server_tools_machine

Machine-level server configuration: base packages, editor alternatives, XDG variables
and shell configuration.

## Supported distributions

Ubuntu LTS, Debian, and Rocky Linux / RHEL-compatible. Package lists are keyed by
distribution and selected from facts, with a family fallback.

## Requirements

- Collection `community.general` (for `alternatives`)
- Root privileges

## Role variables

| Variable | Default | Description |
| --- | --- | --- |
| `packages` | per distribution | Package lists keyed `debian`, `ubuntu` and `redhat`. |
| `alternatives` | editor and vim to nvim | Alternatives registered system-wide. |
| `xdg_vars` | see `defaults/main.yml` | XDG variables written to `/etc/profile.d`. |
| `uv_install_script_url` | `https://astral.sh/uv/install.sh` | Vendor installer script used to install `uv` system-wide. |
| `uv_install_dir` | `/usr/local/bin` | Shared install location for the `uv` binary, on every account's `PATH` by default. |

## Dependencies

None declared in `meta/main.yml`.

## Example usage

```yaml
- hosts: centos:debian:ubuntu
  roles:
    - server_tools_machine
```

## Notes

- EPEL is installed first on RedHat family hosts, because several tools are not in the
  base repositories.
- Debian family package selection uses `ansible_distribution` so Ubuntu and Debian can
  differ, falling back to the Debian list when a distribution has no specific entry.
- Setting the login shell is currently disabled, because it fails on GCP hosts using
  OS Login where the shell is managed externally.
- `uv` is not consistently packaged across supported distributions, so it is installed
  system-wide from the vendor's installer script instead of the distro package manager,
  for use by `server_tools_user`'s per-user tool installs.
- `libmagic` (`libmagic1` on Debian family, `file-libs` on RedHat family) is installed
  as a runtime dependency of `install-release`, which `server_tools_user` installs per
  user with `uv`.
