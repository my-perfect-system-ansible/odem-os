---
namespace: odem
collection: os
role: packages
---

# `odem.os.packages`

Configure APT sources, upgrade packages, and set up unattended-upgrades

## Default variables

| Variable | Default | Description |
|---|---|---|
| `packages_enable_auto_upgrades` | `true` | Configure unattended-upgrades for automatic package upgrades |
| `packages_enable_components_contrib` | `true` | Enable the contrib component |
| `packages_enable_components_non_free` | `true` | Enable the non-free component |
| `packages_enable_components_non_free_firmware` | `true` | Enable the non-free-firmware component |
| `packages_enable_deb_src` | `true` | Include deb-src entries for all enabled repository pockets |
| `packages_enable_repos_backports` | `true` | Enable the backports apt repository pocket |
| `packages_enable_repos_main` | `true` | Enable the main apt repository pocket |
| `packages_enable_repos_security` | `true` | Enable the security apt repository pocket |
| `packages_enable_repos_updates` | `true` | Enable the updates apt repository pocket |
| `packages_upgrade` | `dist` | apt upgrade mode applied to all packages; 'no' skips the upgrade |

## Dependencies

- `odem.os.user`

## Example usage

```yaml
- hosts: all
  roles:
    - odem.os.packages
```

## Role metadata

- **Min Ansible version**: `2.16.0`
- **License**: GPL-3.0-or-later
- **Platforms**: Debian (trixie)
- **Tasks file lines**: 10

## Related files

- [`meta/main.yml`](meta/main.yml) — galaxy_info + role dependencies
- [`meta/argument_specs.yml`](meta/argument_specs.yml) — variable spec (the source of the variable table above)
- [`defaults/main.yml`](defaults/main.yml) — variable defaults (the source of the default values above)
