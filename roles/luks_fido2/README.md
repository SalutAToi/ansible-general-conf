# luks_fido2

Configures FIDO2-backed unlocking of the LUKS container using `systemd-cryptenroll`,
including the dracut module needed to load FIDO2 support in the initramfs.

## Supported distributions

Fedora (GNOME) and Pop!_OS (COSMIC). Workstations only.

## Requirements

- Collection `community.general` (for `crypttab`)
- A FIDO2 security key
- An existing LUKS container with a passphrase already enrolled
- Root privileges

## Role variables

| Variable | Default | Description |
| --- | --- | --- |
| `fido2_packages` | per distribution | FIDO2 and dracut packages to install. |
| `tpm_packages` | per distribution | TPM related packages, kept for reference. |
| `dracut.config_dir` | `/etc/dracut.conf.d` | Where dracut drop-in configuration is written. |
| `dracut_fido_workaround` | `11-fido2.conf` | Drop-in that adds the `fido2` dracut module. |

## Dependencies

None declared in `meta/main.yml`.

## Example usage

```yaml
- role: luks_fido2
```

## Manual steps

`systemd-cryptenroll` asks for the existing LUKS passphrase, so the role pauses and
prints the command to run:

```bash
systemd-cryptenroll --fido2-device=auto /dev/<device>
```

## Notes

- The `fido2` dracut module is not pulled in automatically on either distribution,
  which is why the drop-in is shipped in `files/`.
- Keep a passphrase keyslot as a fallback.
- For TPM-backed unlocking instead of a security key, see `luks_tpm`.
