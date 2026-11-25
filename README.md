# `odem.os` Ansible Collection

Operating system init + the shared per-user identity model for Debian 13
(trixie) and Fedora 45: package sources, unattended upgrades, locale /
keyboard / console / timezone, core CLI tooling — plus users, groups,
SSH keys, sudoers, group folders (the merged `odem.os.user` role).

## Galaxy metadata

- **namespace**: `odem`
- **name**: `os`
- **version**: `0.4.0`
- **dependencies**: `ansible.posix`

See [`galaxy.yml`](galaxy.yml) for the canonical values.

## Roles

| Role | Purpose |
|---|---|
| [`odem.os.system_tools`](roles/system_tools/README.md) | Apt-by-category groups for archives, build, file-manager, network, transfer, statistics tools. Per-area toggles. |
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
    - odem.os.package_manager
    - odem.os.system_settings
    - odem.os.system_tools
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
