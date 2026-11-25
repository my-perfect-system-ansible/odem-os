# AGENTS.md — odem-os

Operating system init + the shared per-user identity model for Debian 13
(trixie) and Fedora 45: package sources, unattended upgrades, locale /
keyboard / console / timezone, core CLI tooling, users, groups, SSH keys,
sudoers.

## Galaxy

- **namespace**: `odem`
- **name**: `os`
- **version**: `0.4.0`
- **dependencies**: `ansible.posix >=1.0.0`

## Roles

| Role | Description | Complexity |
|---|---|---|
| `odem.os.package_manager` | Configure `/etc/apt/sources.list` (4 pockets × components × deb-src), install base apt packages, configure unattended-upgrades. | 2 |
| `odem.os.system_settings` | Configure hostname + `/etc/hosts` (custom entries), locale, keyboard, console (kmscon / plymouth), timezone / chrony, and a `tools.yml` sub-step (install/remove packages + static `/etc/resolv.conf` nameserver). One sub-file per concern. | 2 |
| `odem.os.system_tools` | Apt-by-category groups (archives, build, core, file-manager, network, transfer, statistics). Each gated by a `<group>_enable_<area>` toggle. | 1 |
| `odem.os.user` | Merged per-user role (2026-11: former `odem.base.identity` + `odem.users.users`/`groups`/`ssh`/`sudo`). Orchestrator `tasks/main.yml`: platform assert → `identity.yml` (resolve protocol, produces `user_resolved` / `user_present` / `user_absent` / `user_group_names`) → `folders.yml` (shared group folders, gated by `user_enable_group_folders`) → `groups.yml` → `users.yml` (password map, root special path, create/remove) → `ssh.yml` → `sudo.yml`. One tasks file per subpart — no `_add`/`_del`/`_facts` splits. | 3 |
## Defaults (system-wide)

- Every role in this collection depends on `odem.os.user`; every per-user role in the other collections declares `odem.os.user` in its `meta/main.yml` (Galaxy dependency `odem.os`). The `odem_*` filter plugins live in `plugins/filter/` here — referenced fully-qualified as `odem.os.odem_*` (bare filter names don't resolve).
- `system_tools` has 7 sub-area toggles (`archives`, `build`, `core`, `fm`, `net`, `transfer`, `stats`); defaults to all true.

## Conventions

- **Multi-file sub-areas are intentional** — `system_settings` and `system_tools` are organized by concern (one file per sub-area, e.g. `system_settings` `hostname.yml` / `tools.yml`), not by phase. Flattening would mix unrelated configs.
- **`package_manager` toggle pattern** — `package_manager_enable_repos_{main,updates,backports,security}`, `package_manager_enable_auto_upgrades`. Disabled sub-steps skip without effect.
