# program_config

Configures user programs from a bare dotfiles repository checked out over the home
directory. Also provides the shared task file used by other roles to re-apply dotfiles
after they clone a tool into a dotfiles-managed path.

## Supported distributions

Distribution agnostic. The role only needs `git` and a home directory.

## Requirements

- `git` installed on the target

## Role variables

| Variable | Default | Description |
| --- | --- | --- |
| `dotfiles.repo` | `https://github.com/SalutAToi/dotfiles.git` | Dotfiles repository. |
| `dotfiles.bare_location` | `$XDG_CONFIG_HOME/dotfiles` | Where the bare repository is stored. |
| `dotfiles.work_tree` | `$HOME` | Work tree the repository is checked out over. |

`reapply_dotfiles.yml` additionally expects `reapply_path`, the path whose tracked
files should be restored.

## Dependencies

None declared in `meta/main.yml`.

## Example usage

Run the role for a user:

```yaml
- role: program_config
```

Re-apply dotfiles after cloning a tool over a tracked path:

```yaml
- name: Réapplication des dotfiles sur les chemins clonés
  ansible.builtin.include_role:
    name: program_config
    tasks_from: reapply_dotfiles
  vars:
    reapply_path: "{{ item.dest }}"
  loop: "{{ git_tools }}"
```

## Notes

- Both profiles check out the same repository and branch. The role is run once inside
  each profile play, so `$HOME` resolves to the account being configured.
- The bare repository plus detached work tree pattern requires the `git` CLI. The
  `git` module cannot drive `--git-dir` and `--work-tree`, which is why
  `command-instead-of-module` is skipped for this repository.
