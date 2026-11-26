# `odem.os` Ansible Collection

Operating system init + the shared per-user identity model + system tweaks for
Debian 13 (trixie) and Fedora 45: package sources, unattended upgrades,
locale / keyboard / console / timezone, core CLI tooling — plus users, groups,
SSH keys, sudoers, group folders (the merged `odem.os.user` role), zram /
tmpfs ramdisks / kmscon (the merged `odem.os.tweaks` role), and per-user
backup restore (the merged `odem.os.backup` role).

## Galaxy metadata

- **namespace**: `odem`
- **name**: `os`
- **version**: `0.6.0`
- **dependencies**: `ansible.posix`

See [`galaxy.yml`](galaxy.yml) for the canonical values.

## Roles

| Role | Purpose |
|---|---|
| [`odem.os.backup`](roles/backup/README.md) | Per-user restore — SSH keys/config/authorized_keys, Brave bookmarks (gated by `backup_enable_persist_brave`), home directory; conservative `synchronize`, `delete: false`. Merged from the deleted `odem.backup` collection. |
| [`odem.os.packages`](roles/packages/README.md) | Configure APT sources (4 pockets × components × deb-src), install base packages, configure unattended-upgrades. |
| [`odem.os.settings`](roles/settings/README.md) | Configure hostname + `/etc/hosts` (custom entries), locale, keyboard, console, timezone — one tasks file per concern, plus a `tools.yml` sub-step (install/remove packages + static `/etc/resolv.conf` nameserver). |
| [`odem.os.tools`](roles/tools/README.md) | Apt-by-category groups for archives, build, file-manager, network, transfer, statistics tools. Per-area toggles. |
| [`odem.os.tweaks`](roles/tweaks/README.md) | Merged system tweaks — zram compressed swap, tmpfs ramdisks with backup/restore, kmscon userspace console. One tasks file per subpart, gated by `tweaks_enable_*` toggles. |
| [`odem.os.user`](roles/user/README.md) | Merged per-user role — identity resolve protocol (`user_resolved`/`user_present`/`user_absent`/`user_group_names`), ecosystem-wide `user_default_*` globals, groups, accounts (root special path), SSH keys, sudoers, shared group folders. Also ships the `odem_*` filter plugins under `plugins/filter/`. |
## Installation

```bash
ansible-galaxy collection install odem.os
```

## Usage

```yaml
- hosts: all
  become: true
  roles:
    - odem.os.packages
    - odem.os.settings
    - odem.os.tools
```

## Host prerequisites

Every role in this collection declares `odem.os.user` as a
dependency, so the play fails fast on unsupported platform families.
The other collections' per-user roles depend on `odem.os.user` too.

## Documentation

- [`AGENTS.md`](AGENTS.md) — developer-facing conventions
- `roles/<role>/README.md` — per-role variable docs

## License

GPL-3.0-or-later
