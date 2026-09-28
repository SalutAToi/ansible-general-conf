# sources

Configures third-party package repositories and their signing keys for workstations.

## Supported distributions

Fedora (GNOME) and Pop!_OS (COSMIC). Repository definitions are applied per
`ansible_os_family`.

## Requirements

- Collection `community.general` (for `copr`)
- `group_vars` providing `package_arch` for Debian family hosts

## Role variables

| Variable | Default | Description |
| --- | --- | --- |
| `deb_reqs` | see `vars/main.yml` | Packages required before adding apt repositories. |
| `rpm_reqs` | see `vars/main.yml` | Packages required before adding dnf repositories. |
| `deb_repos` | ProtonVPN, keyd PPA | deb822 repository definitions, each with `signed_by`. |
| `rpm_repos` | ProtonVPN, Speedtest CLI | yum repository definitions with `gpgkey`. |
| `copr_projects` | scrcpy, keyd | COPR repositories to enable on Fedora. |

## Dependencies

None declared in `meta/main.yml`.

## Example usage

```yaml
- role: sources
```

## Notes

- Debian family repositories use `deb822_repository` with `signed_by`. `apt_key` and
  `apt-key add` are deprecated and are not used.
- Repository URLs use the vendor's documented endpoint, with `$releasever` or
  `$basearch` where the vendor supports it.
- `keyd` is not packaged for the Ubuntu LTS releases this repo targets, so its own
  backport PPA (`keyd-team/ppa`) is used on Pop!_OS instead of the default archives.
