# pam

Configures PAM for YubiKey-backed authentication through `pam_u2f`, and registers a
U2F mapping for each workstation account.

## Supported distributions

Fedora (GNOME) and Pop!_OS (COSMIC). Packages and PAM edits are selected from
`ansible_os_family`.

## Requirements

- Collection `community.general` (for `pamd`)
- `yubikey_pin` provided by `vault.yml`
- A YubiKey physically present during the run

## Role variables

| Variable | Default | Description |
| --- | --- | --- |
| `u2f_mappings_file` | `/etc/u2f_mappings` | System-wide file holding one mapping line per user. |
| `u2f_users` | `christophe_perso`, `christophe_work` | Accounts to enroll, each with its `key_pin`. |

`u2f_packages` and `pam_config` live in `vars/main.yml`.

## Dependencies

None declared in `meta/main.yml`.

## Example usage

```yaml
- role: pam
```

## Manual steps

Enrollment is interactive. For each user not already present in
`u2f_mappings_file`, `pamu2fcfg` runs and the YubiKey must be touched once. Users
already listed in the mappings file are skipped, so re-running the role does not
prompt again.

## Notes

- Both profiles get their own mapping line in the same system-wide file.
- `pinverification` is currently `0` for the sudo rule. Raise it if the key is
  configured with a PIN that should be demanded on every sudo.
