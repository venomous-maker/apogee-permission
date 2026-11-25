# apogee-permission

Roles and permissions for [Apogee](https://github.com/venomous-maker) apps,
stored in the database and cached, wired into the gate and the router.

In an apogee-framework app, importing the module is all it takes:
`PermissionPackage` adds the access tables' migration (run it with `migrate`),
lets the gate answer permissions, registers the `role:` and `permission:`
middleware, and adds `permission:create-role` and `permission:show`.

```typescript
import "std"
import "apogee-permission"
use apogee.*

Access.role("editor").grant(["posts.edit", "posts.publish"])
Access.role("moderator").grant(["comments.*"])       // everything under comments.
Access.assignRole(user, "editor")
Access.givePermission(user, "reports.view")          // directly, without a role

Access.can(user, "posts.publish")                    // true
Access.hasRole(user, "editor")                       // true
Access.permissionsOf(user)                           // ["posts.edit", "posts.publish", "reports.view"]

gate.allows(user, "posts.publish")                   // and can: middleware
router.get("/admin", admin).middleware(["auth", "role:admin|editor"])
router.post("/posts/{post}/publish", publish).middleware(["auth", "permission:posts.publish"])
```

- **Users** are any objects with an id (`Authenticatable`, or an `id` field
  reflection can read), stored as `Class#id`.
- **Wildcards:** a permission ending in `.*` grants everything under it, and
  `*` grants everything. `Access.superRole = "admin"` lets one role do anything.
- **Speed:** each user's roles and permissions are worked out once (a few
  queries) and kept for `cacheSeconds` (60). Any change made through `Access`
  drops what is kept, so a check is a map lookup. Changes made by another
  process show within `cacheSeconds`.
- **The gate:** `registerWith` answers abilities that are permissions before
  the gate's own rules; abilities and policies decide everything else.
- **Middleware:** `role:a|b` and `permission:a|b` pass with any one of those
  listed; 401 without a user, 403 without the access.

## Settings

`permission.cache_seconds` (`PERMISSION_CACHE_SECONDS`, 60),
`permission.super_role` (`PERMISSION_SUPER_ROLE`, none) and
`permission.max_users` (100000: the most users whose access is kept in memory,
the least recently checked going first).

## Stores

`OrbitAccessStore` keeps everything in the app's database as Orbit models
(`access_roles`, `access_permissions`, `access_role_permissions`,
`access_model_roles`, `access_model_permissions`, made by `CreateAccessTables`)
on SQLite, MySQL/MariaDB or PostgreSQL. `MemoryAccessStore` is for tests.
Outside the framework: `Access.use(store)`, `Access.registerWith(gate)`,
`Access.middleware(router)`.

## Tests

```sh
grav test tests/permission.grav
grav test tests/package.grav
PERMISSION_MYSQL_URL=mysql://root:pw@127.0.0.1:3306/db PERMISSION_POSTGRES_URL=postgres://postgres:pw@127.0.0.1:5432/db grav test tests/permission.grav
```

## License

MIT
