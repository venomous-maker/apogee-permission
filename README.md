# apogee-permission

Roles and permissions for [Apogee](https://github.com/venomous-maker) apps,
stored in the database and cached, wired into the gate and the router.

```grav
import "std"
import "apogee-permission"
use apogee.*

Access.use(new DatabaseAccessStore(pool))           // then .createTables(); memory by default
Access.role("editor").grant(["posts.edit", "posts.publish"])
Access.role("moderator").grant(["comments.*"])       // everything under comments.
Access.assignRole(user, "editor")
Access.givePermission(user, "reports.view")          // directly, without a role

Access.can(user, "posts.publish")                    // true
Access.hasRole(user, "editor")                       // true
Access.permissionsOf(user)                           // ["posts.edit", "posts.publish", "reports.view"]

Access.registerWith(gate)                            // gate.allows(user, "posts.publish"), and can: middleware
Access.middleware(router)                            // role: and permission:
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

## Stores

`MemoryAccessStore` (one process) and `DatabaseAccessStore(pool, prefix = "access_")`:
five tables (`roles`, `permissions`, `role_permissions`, `model_roles`,
`model_permissions`) on SQLite, MySQL/MariaDB or PostgreSQL.

## Tests

```sh
grav test tests/permission.grav
PERMISSION_MYSQL_URL=mysql://root:pw@127.0.0.1:3306/db PERMISSION_POSTGRES_URL=postgres://postgres:pw@127.0.0.1:5432/db grav test tests/permission.grav
```

## License

MIT
