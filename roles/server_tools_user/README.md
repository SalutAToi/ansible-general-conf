# server_tools_user

User-level server configuration: git-based tools and pipx applications. Dotfiles are
handled by the shared `dotfiles` role, run before this one.

## Supported distributions

Distribution agnostic. Relies on packages installed by `server_tools_machine`.

## Requirements

- `git` and `pipx` installed, which `server_tools_machine` provides
- Runs unprivileged, as the target user
- The `dotfiles` role, run first so its `dotfiles` variable and checkout are available

## Role variables

| Variable | Default | Description |
| --- | --- | --- |
| `git_tools` | tpm, LazyVim starter | Tool repositories to clone, with `delete_git_folder`. |
| `pipx_tools` | `[]` | pipx applications to install, with optional injected packages. |
| `alternatives` | editor and vim to nvim | Alternatives, applied only where permitted. |

## Dependencies

None declared in `meta/main.yml`. The role calls `dotfiles` through `include_role`
with `tasks_from: reapply`, so that role must be present and must run first in the
playbook so its variables are already set.

## Example usage

```yaml
- hosts: centos:debian:ubuntu
  roles:
    - dotfiles
    - server_tools_user
```

## Notes

- Tool directories are removed before cloning and the dotfiles checkout is re-applied
  afterwards, so a clone cannot shadow dotfiles-tracked files.
- `delete_git_folder` detaches a cloned starter config from upstream so it can be
  tracked in the user's own dotfiles. This is what LazyVim expects.
