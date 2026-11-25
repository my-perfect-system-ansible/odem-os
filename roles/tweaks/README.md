---
namespace: odem
collection: os
role: tweaks
---

# `odem.os.tweaks`

System-level performance and console tweaks — the merged former
`odem.optimize` roles (`zram`, `ramdisks`, `kmscon`). Orchestrator
`tasks/main.yml` includes one file per subpart, each gated by its own
`tweaks_enable_*` toggle (default `true`):

| Subpart | Toggle | Purpose |
|---|---|---|
| `zram.yml` | `tweaks_enable_zram` | Compressed swap in RAM — `zram-tools` + `/etc/default/zramswap` on Debian, `zram-generator` + `/etc/systemd/zram-generator.conf` on RedHat. |
| `ramdisks.yml` | `tweaks_enable_ramdisks` | Mount a path as tmpfs with backup/restore — fstype detection inline, conversion block (backup → fstab entry → mount → restore) gated by the detected state. |
| `kmscon.yml` | `tweaks_enable_kmscon` | Userspace console — `kmscon` + `libtsm4` from `trixie-backports` on Debian, `kmscon` via dnf on RedHat; deploys `files/etc/kmscon/kmscon.conf`. |

## Default variables

| Variable | Default | Description |
|---|---|---|
| `tweaks_zram_percentage` | `10` | Percentage of total RAM for zram swap |
| `tweaks_zram_algo` | `zstd` | Compression algorithm (zstd, lz4, lzo) |
| `tweaks_zram_config_path` | `/etc/default/zramswap` | zramswap config file (Debian) |
| `tweaks_zram_service` | `zramswap` | zramswap systemd service (Debian) |
| `tweaks_ramdisks_mount` | `/var/log` | Path to mount as tmpfs |
| `tweaks_ramdisks_opts` | `mode=1777,nosuid,nodev` | tmpfs mount options |
| `tweaks_ramdisks_backup_path` | `/tmp/var-log-backup` | Backup location for pre-existing contents |
| `tweaks_kmscon_default_release` | `trixie-backports` | apt target release (Debian) |
| `tweaks_kmscon_config_dir` | `/etc/kmscon` | Config deploy directory |

Full option list: [`meta/argument_specs.yml`](meta/argument_specs.yml).

## Dependencies

None — system-wide role, no per-user logic.

## Example usage

```yaml
- hosts: all
  become: true
  roles:
    - odem.os.tweaks
```

## Role metadata

- **Min Ansible version**: `2.16.0`
- **License**: GPL-3.0-or-later
- **Platforms**: Debian (trixie), Fedora (45)
- **Tasks**: `main.yml` (orchestrator) → `zram.yml`, `ramdisks.yml`, `kmscon.yml`

## Related files

- [`meta/main.yml`](meta/main.yml) — galaxy_info + role dependencies
- [`meta/argument_specs.yml`](meta/argument_specs.yml) — variable spec
- [`defaults/main.yml`](defaults/main.yml) — variable defaults
- [`files/etc/kmscon/kmscon.conf`](files/etc/kmscon/kmscon.conf) — deployed kmscon config