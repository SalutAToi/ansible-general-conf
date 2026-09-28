# workstation_packages

Installs workstation software: distribution packages, flatpaks, Nerd Fonts and
git-based user tools.

## Supported distributions

Fedora (GNOME) and Pop!_OS (COSMIC). Package lists are keyed by distribution and
selected from facts.

## Requirements

- Collection `community.general` (for `flatpak` and `flatpak_remote`)
- Network access to the GitHub API to resolve the current Nerd Fonts, install-release
  and qwerty-fr releases
- The `dotfiles` role, whose `reapply` tasks are reused after cloning tools
- On Pop!_OS, `qwerty-fr` is installed through `install-release`'s `--pkg` mode, so
  `install_release.yml` must run before `qwerty_fr.yml` (it already does, in
  `tasks/system.yml`)

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
| `install_release_repo` | `Rishang/install-release` | Repository queried for the current release. |
| `install_release_install_dir` | `/usr/local/bin` | Shared install location for the `ir` binary, on every account's `PATH` by default. |
| `qwerty_fr_repo` | `qwerty-fr/qwerty-fr` | Repository queried for the current release. |

Package lists and `git_tools` live in `vars/main.yml`.

## Dependencies

None declared in `meta/main.yml`. The role calls `dotfiles` through `include_role`
with `tasks_from: reapply`, so that role must be present.

## Scopes

- `system` — distribution packages, flatpaks, fonts, `install-release` and `qwerty-fr`.
  Requires root.
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
- `install-release` (the `ir` command) is resolved and installed the same way as Nerd
  Fonts: latest release queried from the GitHub API, installed system-wide once so
  both profiles share it, with the installed version recorded next to the binary.
- `qwerty-fr` installs from a real `.deb` on Pop!_OS via `install-release` (`ir get
  --pkg`), which registers the layout in GNOME/COSMIC's input source list
  automatically. Fedora has no upstream package, so
  only the vendor-documented "other distros" method is used (extracting the release
  zip at the filesystem root); this does **not** register the layout, so on Fedora you
  must add it manually once: Settings → Keyboard → Input Sources → Add an "Other"
  source, or run `gsettings set org.gnome.desktop.input-sources sources "[('xkb',
  'us+qwerty-fr')]"` (adjust if you already have other input sources configured).
