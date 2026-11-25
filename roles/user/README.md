---
namespace: odem
collection: os
role: user
---

# `odem.os.user`

Merged per-user role — the former `odem.base.identity` anchor role plus the
former `odem.users.users`/`groups`/`ssh`/`sudo` roles. Fails fast on
non-Debian/RedHat hosts, carries the ecosystem-wide global defaults
(`user_default_*`), resolves the per-user identity model, and manages
groups, user accounts (including the root special path), SSH keys,
sudoers, and shared group folders with setgid group inheritance.

## Global defaults

| Variable | Default | Description |
|---|---|---|
| `user_default_repo_dir` | `/srv/repo` | Base directory for cloned repos |
| `user_default_repo_dir_mps` | `/srv/repo/github/my-perfect-system/mps` | Base directory for cloned my-perfect-system repos |
| `user_default_file_mode` | `0600` | Default file mode |
| `user_default_dir_mode` | `0700` | Default directory mode |
| `user_default_script_mode` | `0700` | Default script mode |
| `user_default_owner` | `root` | Default file owner |
| `user_default_group` | `root` | Default file group |
| `user_default_suid_mode` | `2770` | Default setuid mode |

## Feature toggles

| Variable | Default | Description |
|---|---|---|
| `user_enable_group_folders` | `true` | Toggle for the shared group folders sub-step (empty list = no-op) |
| `user_enable_group_extraction` | `true` | Extract each present user's `group`/`groups` fields into the ensure-present group set |
| `user_enable_print_passwords_plaintext` | `false` | Print generated passwords and hashes to stdout (debug only) |

## Shared group folders

| Variable | Default | Description |
|---|---|---|
| `user_group_folders` | `[]` | List of dicts — each entry creates a directory with `path` (required), `group` (required, ensured present), and optional `mode` override |
| `user_group_folders_mode` | `2770` | Default directory mode — setgid bit so new subfolders inherit the group |

## Identity model

| Variable | Default | Description |
|---|---|---|
| `user_catalog` | `{}` | Identity catalog keyed by a free-form unique id; see `meta/argument_specs.yml` for the full per-entity schema |
| `user_list` | `[]` | Assignment list of `{name, state}` pairs referencing `user_catalog` keys |

Resolved facts: `user_resolved`, `user_present`, `user_absent`,
`user_group_names`.

## Group / sudo management

| Variable | Default | Description |
|---|---|---|
| `user_groups_list` | `[]` | Explicit list of group dicts (`name`, `state`, `gid`, `system`) |
| `user_sudo_macros` | `{ ADMIN_DEFAULT: …, ADMIN_DEBUG: …, ADMIN_MINIMAL: … }` | Cmnd_Alias macro name → command patterns, rendered into the shared macros file |
| `user_sudo_macros_file` | `/etc/sudoers.d/00_macros` | Path of the shared Cmnd_Alias macros file |

## Dependencies

None — this is the anchor role of the per-user dependency chain.

## Example usage

```yaml
- hosts: all
  roles:
    - odem.os.user
```

## Role metadata

- **Min Ansible version**: `2.16.0`
- **License**: GPL-3.0-or-later
- **Platforms**: Debian (trixie), Fedora (45)
- **Tasks**: `main.yml` (orchestrator) → one file per subpart: `identity.yml`,
  `folders.yml`, `groups.yml`, `users.yml`, `ssh.yml`, `sudo.yml` — add/del/facts
  logic lives inline behind task-level `when:` checks

## Related files

- [`meta/main.yml`](meta/main.yml) — galaxy_info + role dependencies
- [`meta/argument_specs.yml`](meta/argument_specs.yml) — variable spec (the source of the variable table above)
- [`defaults/main.yml`](defaults/main.yml) — variable defaults (the source of the default values above)