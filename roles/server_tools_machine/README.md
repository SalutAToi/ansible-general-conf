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
