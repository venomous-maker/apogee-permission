# Changelog

Every release of apogee-permission, newest first. Each is a `vX.Y.Z` tag with a GitHub
release carrying its section; the `grav` line in `grav.toml` says which
compiler versions it builds with.

## Unreleased

- `Access`: roles and permissions for any user, wildcards, a super role, a
  cache that changes drop; the gate hook and the role: and permission: middleware.
- The access tables as Orbit models with their migration; an Orbit store
  (SQLite, MySQL, PostgreSQL) and a memory one.
- Users' access is kept in a bounded LruCache (`permission.max_users`); needs
  grav 1.13.0 or newer.
- `PermissionPackage`: connects itself to an apogee-framework app (settings,
  the gate, the middleware, the migration, `permission:create-role`,
  `permission:show`).
