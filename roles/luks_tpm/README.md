# luks_tpm

Configures TPM-backed automatic unlocking of the LUKS container using
`systemd-cryptenroll`.

## Supported distributions

Fedora (GNOME) and Pop!_OS (COSMIC). Workstations only. Packages and the initramfs
rebuild command are selected from `ansible_os_family`.

## Requirements

- Collection `community.general` (for `crypttab`)
- A machine with a TPM 2.0 device
- An existing LUKS container with a passphrase already enrolled
- Root privileges

## Role variables

| Variable | Default | Description |
| --- | --- | --- |
| `luks_tpm_crypttab_opts` | `tpm2-device=auto` | Option added to the crypttab entry. |
| `luks_tpm_pcrs` | `7` | PCRs the key is bound to. PCR 7 tracks Secure Boot state. |
| `tpm_packages` | `tpm2-tools` plus family specifics | Packages installed per distribution. |

## Dependencies

None declared in `meta/main.yml`.

## Example usage

```yaml
- role: luks_tpm
```

## Manual steps

`systemd-cryptenroll` asks for the existing LUKS passphrase, so it cannot run
unattended. The role detects the LUKS device, configures `crypttab`, then pauses and
prints the exact command to run:

```bash
systemd-cryptenroll --tpm2-device=auto --tpm2-pcrs=7 /dev/<device>
```

The role checks `cryptsetup luksDump` for an existing `systemd-tpm2` token first, so
the prompt and the initramfs rebuild are skipped once the key is enrolled.

## Notes

- Binding to more PCRs increases the chance the unlock breaks after a firmware or
  bootloader update. PCR 7 alone is the conservative default.
- `systemd-cryptenroll` is preferred over `clevis`, matching `luks_fido2` and avoiding
  an extra daemon.
- Keep a passphrase keyslot. If the TPM state changes, it is the only way back in.
