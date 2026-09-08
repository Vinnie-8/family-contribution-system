# Roles & Permissions

Role assignment is **many-to-many** — a member can hold multiple roles at once (e.g. a Chairman who is also a contributing Member, or a Treasurer who is also a Guardian). Roles are never a single fixed field on a user record.

| Role | Description | Key Permissions |
|---|---|---|
| **Member** | Any active adult family member | View aggregate campaign/event totals, view own contribution history, make pledges/payments, view meeting minutes, download own statements |
| **Chairman** | Top-level admin | Create/close campaigns and events, set AGM contribution rules, approve resolutions, assign/remove admins, activate/deactivate members, elect/confirm new leadership terms |
| **Secretary** | Meetings & documentation admin | Create/manage meetings, record/edit/publish/lock minutes, manage attendance, send meeting notifications, upload and publish event budgets |
| **Treasurer** | Finance admin | Confirm/reject payments, manually record bank/cash/in-kind payments, manage partial payments, record expenses, issue receipts, generate financial and annual reports, track defaulters, record payments/pledges on behalf of minors |
| **Guardian** | Parent/guardian of a minor member | Make pledges and payments on behalf of their linked child — **no co-approval required** |
| **Minor Member** *(no login)* | Child under 18, linked to a Guardian | No independent account or credentials. Pledges/payments recorded on their behalf by their Guardian, or by Treasurer/Chairman for cash handed over in person |

!!! note "Leadership terms"
    Chairman, Secretary, and Treasurer are **elected positions with a fixed term (every 3 years)**. Every handover — whether from a scheduled election or an early resignation — goes through the same `HandoverRecord` flow described in [Functional Requirements](functional-requirements.md#310-role-handover--leadership-terms). This keeps "who held what role, and when" fully auditable across the family's history, not just for the current term.

## Permission Boundaries Worth Highlighting

- **Privacy is member-facing, not admin-facing.** The rule that individual contribution amounts are hidden applies *between members*. Treasurer and Chairman always retain full visibility — they need it to reconcile payments, confirm disputes, and produce reports.
- **Guardians act independently.** No Chairman/Treasurer co-approval is required for a Guardian pledging or paying on behalf of their linked minor — but every such action is still attributed to the Guardian in the audit trail, with the minor recorded as the beneficiary.
- **Minors never receive login credentials.** There is no path — now or later — where a minor authenticates directly. This is a deliberate constraint, not a gap to "fix."
