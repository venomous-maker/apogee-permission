# Changelog

Every release of apogee-permission, newest first. Each is a `vX.Y.Z` tag with a GitHub
release carrying its section; the `grav` line in `grav.toml` says which
compiler versions it builds with.

## Unreleased

### Fixes

- Database connections that the server dropped while idle (MySQL's `wait_timeout`, a restart, a proxy's idle limit) reconnect on their next use instead of failing with "server has gone away" (grav-db 0.2.1). Uses the new releases of apogee-console, apogee-package, grav-db, grav-orbit.

## 0.1.0 (2026-11-25)

- `Access`: roles and permissions for any user, wildcards, a super role, a
  cache that changes drop; the gate hook and the role: and permission: middleware.
- The access tables as Orbit models with their migration; an Orbit store
  (SQLite, MySQL, PostgreSQL) and a memory one.
- Users' access is kept in a bounded LruCache (`permission.max_users`); needs
  grav 1.13.0 or newer.
- `PermissionPackage`: connects itself to an apogee-framework app (settings,
  the gate, the middleware, the migration, `permission:create-role`,
  `permission:show`).

### Dependencies

- Uses apogee-http 0.3.0, apogee-routing 0.3.0, apogee-auth 0.2.2, apogee-console 0.2.2, apogee-package 0.1.0, grav-orbit 0.3.0, apogee-testing 0.1.1 (from their GitHub tags).
