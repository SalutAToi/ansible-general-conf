# workstation_packages

Installs workstation software: distribution packages, flatpaks, Nerd Fonts and
git-based user tools.

## Supported distributions

Fedora (GNOME) and Pop!_OS (COSMIC). Package lists are keyed by distribution and
selected from facts.

## Requirements

- Collection `community.general` (for `flatpak` and `flatpak_remote`)
- Network access to the GitHub API to resolve the current Nerd Fonts release
- `program_config`, whose `reapply_dotfiles` tasks are reused after cloning tools

## Role variables

| Variable | Default | Description |
| --- | --- | --- |
| `workstation_packages_scope` | `system` | `system`, `user` or `all`. Selects which half of the role runs. |
| `nerd_fonts_repo` | `ryanoasis/nerd-fonts` | Repository queried for the current release. |
| `nerd_fonts_install_dir` | `/usr/share/fonts/nerd-fonts` | Where font archives are extracted. |
| `nerd_fonts` | `[Hack]` | Font archives to install, without the `.zip` suffix. |
| `flatpak_remote_name` | `flathub` | Flatpak remote to configure. |
| `flatpak_remote_url` | Flathub repo URL | Flatpak remote definition URL. |
| `flatpak_packages` | Steam, Tor Browser, Signal | Flatpak applications to install. |

Package lists and `git_tools` live in `vars/main.yml`.

## Dependencies

None declared in `meta/main.yml`. The role calls `program_config` through
`include_role` with `tasks_from: reapply_dotfiles`, so that role must be present.

## Scopes

- `system` — distribution packages, flatpaks and fonts. Requires root.
- `user` — git-based tools cloned into the user's config directory.

## Example usage

```yaml
- role: workstation_packages
  workstation_packages_scope: system
```

## Notes

- Nerd Fonts are resolved from the latest upstream release rather than a pinned URL.
  The installed version is recorded in `.nerd-fonts-version` inside the install
  directory, and the download is skipped when it already matches.
- The font cache is refreshed by a handler after any font change.
- Tool directories are removed before cloning and the dotfiles checkout is re-applied
  afterwards, so a clone cannot shadow dotfiles-tracked files.
