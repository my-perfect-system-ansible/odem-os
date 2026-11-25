# AGENTS.md — odem-os

Operating system init + the shared per-user identity model for Debian 13
(trixie) and Fedora 45: package sources, unattended upgrades, locale /
keyboard / console / timezone, core CLI tooling, users, groups, SSH keys,
sudoers.

## Galaxy

- **namespace**: `odem`
- **name**: `os`
- **version**: `0.5.0`
- **dependencies**: `ansible.posix >=1.0.0`

## Roles

| Role | Description | Complexity |
|---|---|---|
| `odem.os.packages` | Configure `/etc/apt/sources.list` (4 pockets × components × deb-src), install base apt packages, configure unattended-upgrades. | 2 |
| `odem.os.settings` | Configure hostname + `/etc/hosts` (custom entries), locale, keyboard, console (kmscon / plymouth), timezone / chrony, and a `tools.yml` sub-step (install/remove packages + static `/etc/resolv.conf` nameserver). One sub-file per concern. | 2 |
| `odem.os.tools` | Apt-by-category groups (archives, build, core, file-manager, network, transfer, statistics). Each gated by a `tools_enable_<area>` toggle. | 1 |
| `odem.os.tweaks` | Merged system tweaks (2026-11: former `odem.optimize.{zram,ramdisks,kmscon}`). Orchestrator includes one file per subpart, each gated by a `tweaks_enable_*` toggle: `zram.yml` (zram-tools/zramswap on Debian, zram-generator on RedHat), `ramdisks.yml` (fstype detection inline, conversion block: backup → fstab entry → mount → restore), `kmscon.yml` (backports on Debian, dnf on RedHat, deploys `files/etc/kmscon/kmscon.conf`). Variables use the `tweaks_<subpart>_*` prefix. | 2 |
## Defaults (system-wide)

- Every role in this collection depends on `odem.os.user`; every per-user role in the other collections declares `odem.os.user` in its `meta/main.yml` (Galaxy dependency `odem.os`). The `odem_*` filter plugins live in `plugins/filter/` here — referenced fully-qualified as `odem.os.odem_*` (bare filter names don't resolve).
- `tools` has 7 sub-area toggles (`archives`, `build`, `core`, `fm`, `net`, `transfer`, `stats`); defaults to all true.

## Conventions

- **Multi-file sub-areas are intentional** — `settings` and `tools` are organized by concern (one file per sub-area, e.g. `settings` `hostname.yml` / `tools.yml`), not by phase. Flattening would mix unrelated configs.
- **`packages` toggle pattern** — `packages_enable_repos_{main,updates,backports,security}`, `packages_enable_auto_upgrades`. Disabled sub-steps skip without effect.
