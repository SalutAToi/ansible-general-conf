# server_tools_user

User-level server configuration: dotfiles checkout, git-based tools and pipx
applications.

## Supported distributions

Distribution agnostic. Relies on packages installed by `server_tools_machine`.

## Requirements

- `git` and `pipx` installed, which `server_tools_machine` provides
- Runs unprivileged, as the target user

## Role variables

| Variable | Default | Description |
| --- | --- | --- |
| `dotfiles.repo` | `https://github.com/SalutAToi/dotfiles.git` | Dotfiles repository. |
| `dotfiles.bare_location` | `$XDG_CONFIG_HOME/dotfiles` | Where the bare repository is stored. |
| `dotfiles.work_tree` | `$HOME` | Work tree the repository is checked out over. |
| `git_tools` | tpm, LazyVim starter | Tool repositories to clone, with `delete_git_folder`. |
| `pipx_tools` | `[]` | pipx applications to install, with optional injected packages. |
| `alternatives` | editor and vim to nvim | Alternatives, applied only where permitted. |

`reapply_dotfiles.yml` additionally expects `reapply_path`.

## Dependencies

None declared in `meta/main.yml`.

## Example usage

```yaml
- hosts: centos:debian:ubuntu
  roles:
    - server_tools_user
```

## Notes

- Tool directories are removed before cloning and the dotfiles checkout is re-applied
  afterwards, so a clone cannot shadow dotfiles-tracked files.
- `delete_git_folder` detaches a cloned starter config from upstream so it can be
  tracked in the user's own dotfiles. This is what LazyVim expects.
- The role keeps its own copy of `reapply_dotfiles.yml` rather than reusing
  `program_config`, so a server host never depends on a workstation role.
