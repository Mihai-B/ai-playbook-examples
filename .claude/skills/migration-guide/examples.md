# Migration Guide Examples

## Example 1: Add a new enum field

**Input**: `Add a "role" field to the User model with values admin, editor, viewer`

**Output**:

```
## Migration Plan: Add user role field

### Phase 1: Add column with default (this release)

**Schema change** (prisma/schema.prisma):

```prisma
model User {
  id    String   @id @default(cuid())
  email String   @unique
  name  String
  role  UserRole @default(viewer)
  // ... existing fields
}

enum UserRole {
  admin
  editor
  viewer
}
```

**Migration name**: `add-user-role-field`
**Run**: `npx prisma migrate dev --name add-user-role-field`

### Data Backfill
No backfill needed — existing users default to `viewer` role.
To set specific users as admin:

```sql
UPDATE "User" SET role = 'admin' WHERE email IN ('admin@example.com');
```

### Rollback Strategy

```sql
ALTER TABLE "User" DROP COLUMN "role";
DROP TYPE "UserRole";
```

### Checklist
- [ ] Migration runs successfully on local
- [ ] Rollback tested on local
- [ ] Migration applied to staging
- [ ] Data backfill verified on staging
- [ ] No queries reference the column before deployment
```

## Example 2: Two-phase column removal

**Input**: `Remove the deprecated "legacyId" column from the User model`

**Output**:

```
## Migration Plan: Remove User.legacyId (two-phase)

### Phase 1: Remove code references (this release)

No schema changes. Remove all application code that reads or writes `legacyId`:
- `src/services/UserService.ts` — remove `legacyId` from `select` clauses
- `src/api/handlers/users.ts` — remove `legacyId` from response mapping

**Deploy Phase 1 first.** Wait for all instances to be running the new code.

### Phase 2: Drop the column (next release)

**Schema change** (prisma/schema.prisma):
Remove `legacyId String?` from the User model.

**Migration name**: `drop-user-legacy-id`
**Run**: `npx prisma migrate dev --name drop-user-legacy-id`

### Data Backfill
None needed — column is being removed.

### Rollback Strategy

```sql
ALTER TABLE "User" ADD COLUMN "legacyId" TEXT;
```

Note: Data in this column will be lost after Phase 2. Ensure no downstream systems depend on it.

### Checklist
- [ ] Phase 1 deployed and verified (no code references `legacyId`)
- [ ] Migration runs successfully on local
- [ ] Rollback tested on local
- [ ] Confirmed no external systems read this column
```
