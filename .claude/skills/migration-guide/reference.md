# Migration Guide Reference

## Migration Plan Format

```
## Migration Plan: [description]

### Phase N: [action] (this release / next release)

**Schema change** (prisma/schema.prisma):
[Prisma schema diff]

**Migration name**: `descriptive-name`
**Run**: `npx prisma migrate dev --name descriptive-name`

### Data Backfill
[SQL or script to populate data, or "None needed"]

### Rollback Strategy
[SQL to reverse the migration]

### Checklist
- [ ] Migration runs successfully on local
- [ ] Rollback tested on local
- [ ] Migration applied to staging
- [ ] Data backfill verified on staging
- [ ] No queries reference new columns before deployment
```

## Two-Phase Approach for Destructive Changes

### Phase 1 (Release N): Remove code references
- Remove all application code that reads/writes the column
- Deploy code changes
- Column still exists but is unused

### Phase 2 (Release N+1): Drop the column
- Create migration to drop the column
- Test rollback
- Deploy

This prevents downtime during rolling deployments where old and new code run simultaneously.

## Safe Migration Patterns

| Change | Safe? | Notes |
|--------|-------|-------|
| Add nullable column | Yes | No data changes needed |
| Add column with default | Yes | Existing rows get the default |
| Add NOT NULL column without default | No | Must add as nullable first, backfill, then add constraint |
| Drop column | Two-phase | Remove code first, drop column next release |
| Rename column | Two-phase | Add new, migrate data, remove old |
| Add index | Yes | May lock table briefly on large tables |
| Change column type | Risky | Test thoroughly, may require data migration |

## Migration Naming

Good: `add-user-role-field`, `create-comments-table`, `add-email-unique-index`
Bad: `update-schema`, `fix-db`, `migration-1`
