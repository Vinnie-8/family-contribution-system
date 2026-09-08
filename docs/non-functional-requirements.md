# Non-Functional Requirements

| Category | Requirement |
|---|---|
| **Security** | Passwords hashed (bcrypt/argon2); JWT-based auth; role-based access control (RBAC) enforced on every admin endpoint; Minor Members have no auth credentials — no login path is ever exposed for them |
| **Data integrity** | Financial records are append-only/soft-deleted; all money fields use fixed-point/decimal types, never floats; payment confirmation and pledge-fulfillment updates happen inside a single database transaction |
| **Availability** | M-Pesa callback handling (when enabled) is idempotent and retry-safe, built correctly from the start even though it's not live in v1 |
| **Privacy** | Individual contribution amounts are **never** visible to other members — aggregate-only by default and by design. Treasurer/Chairman retain full visibility for administrative and audit purposes |
| **Auditability** | Every financial and admin action is timestamped and attributed to a user; Guardian actions are attributed to the Guardian with the minor recorded as beneficiary |
| **Usability** | Usable by non-technical/elderly users — minimal steps for core actions (pay, view status, view budget) |
| **Scalability** | Schema is multi-tenant-ready (`family_id` scoping throughout) from day one; only one `Family` tenant is active in v1; no tenant-onboarding UI is required yet |
| **Performance** | Aggregate dashboard figures (totals, remaining, % complete) are updated incrementally on each confirmed transaction rather than recomputed with a live `SUM()` on every page load; foreign keys and common filter columns are indexed |
| **Resilience** | Idempotency keys on write endpoints (not just M-Pesa callbacks) to protect against double-submits — common with elderly users retrying an action they're unsure completed |
| **Backup & recovery** | Automated daily backups, stored off the primary database instance, with a documented and periodically tested restore procedure |
| **Maintainability** | Schema changes tracked via versioned migrations (e.g. Alembic) from the first commit, not applied by hand |

!!! tip "Why this matters for a small, real deployment"
    At ~60 members, none of these requirements exist because of scale pressure — they exist because this is **real money belonging to real family members**, much of it handled by non-technical users. Data integrity, auditability, and backup discipline matter *more* at this size, not less, because there's no operations team to catch mistakes early.
