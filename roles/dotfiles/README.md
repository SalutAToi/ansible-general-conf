# dotfiles

Checks out a bare dotfiles repository over the home directory, and provides the shared
task used by other roles to re-apply dotfiles-tracked files after they clone a tool
into a path the dotfiles repo also owns. Used by both workstation and server roles so
the checkout logic is not duplicated per role.

## Supported distributions

Distribution agnostic. Only needs `git` and a home directory.

## Requirements

- `git` installed on the target

## Role variables

| Variable | Default | Description |
| --- | --- | --- |
| `dotfiles.repo` | `https://github.com/SalutAToi/dotfiles.git` | Dotfiles repository. |
| `dotfiles.bare_location` | `$XDG_CONFIG_HOME/dotfiles` | Where the bare repository is stored. |
| `dotfiles.work_tree` | `$HOME` | Work tree the repository is checked out over. |

`tasks/reapply.yml` additionally expects `reapply_paths`, the list of paths whose
tracked files should be restored.

## Dependencies

None declared in `meta/main.yml`.

## Example usage

Check out dotfiles for the current user:

```yaml
- role: dotfiles
```

Re-apply dotfiles after cloning tools over tracked paths:

```yaml
- name: Réapplication des dotfiles sur les chemins clonés
  ansible.builtin.include_role:
    name: dotfiles
    tasks_from: reapply
  vars:
    reapply_paths: "{{ git_tools | map(attribute='dest') | list }}"
```

Pass the whole list and let the task file loop. Do not put a `loop` on the include
itself: a looped include rebinds `item`, and Ansible then fails to resolve the parent
include path when the caller was itself reached through `include_tasks: "{{ item }}"`.

## Notes

- Both workstation profiles, and every server host, check out the same repository and
  branch. The role is run once per account, so `$HOME` resolves to whichever account
  is being configured.
- `git` refuses to check out over pre-existing untracked files (for example a
  `.bashrc` shipped by the base image). Before the first checkout, and before every
  re-apply, the role lists every path the dotfiles repo tracks and removes any that
  already exist on disk, across the whole tree - not one file at a time as errors are
  reported.
- The clone task itself is left to the `git` module's own idempotency (fetch if the
  repo already exists). The checkout is instead gated on the presence of an `index`
  file inside the bare repository, which only appears once a checkout has actually
  succeeded. This means a run that fails partway through checkout is retried in full
  next time, rather than being silently skipped because the bare repo already exists.
- The bare repository plus detached work tree pattern requires the `git` CLI. The
  `git` module cannot drive `--git-dir` and `--work-tree`, which is why
  `command-instead-of-module` is skipped for this repository in `.ansible-lint`.
