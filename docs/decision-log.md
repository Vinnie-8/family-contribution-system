# Decision Log

A running record of design decisions, why they were made, and what's still open — kept so future contributors (or your supervisor) can see the reasoning, not just the current state.

## Resolved

| Decision | Rationale |
|---|---|
| Individual contribution amounts are never shown to other members — aggregate-only, always | Preserves family harmony and privacy while still giving everyone visibility into overall progress |
| v1 targets a single family (~60 members); multi-tenancy is designed into the schema but not built as a user-facing feature | Avoids premature complexity while keeping the door open for scaling to other families later without a schema rewrite |
| Manual payments (bank transfer/cash/in-kind) are the only live method in v1; M-Pesa STK Push is built against the Daraja sandbox but feature-flagged off | Matches how the family currently pays; keeps the M-Pesa integration work usable for learning without risking real transactions on an unproven flow |
| Guardian/Proxy is specifically for members under 18; Guardians act independently, no co-approval required | Matches real family authority structures — a parent doesn't need permission to pledge for their own child |
| Minors have no login/account; pledges/payments are recorded by their Guardian or by Treasurer/Chairman for in-person cash | Minors have no phone numbers to authenticate with; this avoids inventing a workaround identity system for people who structurally can't hold one |
| Campaign ↔ Event is many-to-many | The family's own AGM-funds-the-party pattern shows funding relationships aren't strictly one-to-one |
| Leadership positions run 3-year terms, tracked via `HandoverRecord` with explicit outgoing-officer confirmation | Prevents a permissions gap or overlap during transitions; builds a full leadership history over time |
| Annual reports, standing welfare fund, fines, and beneficiary tracking are included in v3 scope | These are core to how the family actually operates, not optional extras |
| Backups are a stated requirement (daily, tested restore) | This is real financial data for real people; recovery planning isn't optional at any scale |
| Mobile app is deferred; v1 is responsive web only | Keeps initial scope focused; backend/database work is prioritized first regardless |

## Still Open

- If the system later serves multiple families and the same real person belongs to more than one, should identity (one `User` per phone number) be separated from family membership — rather than a member record being tied to a single family the way it is in v1? Not needed while there's one family; worth deciding before a second family ever onboards.
- What's the acceptable latency/retry policy for M-Pesa STK Push failures, once it's actually turned on?
- Should a Minor Member automatically transition to a full adult account (with login) when they turn 18, or does that require a manual re-registration step?
- Should a campaign auto-include minors by default for any campaign type, or should `includes_minors` always default to `false` as an explicit per-campaign opt-in?
- Does a `Budget` need Treasurer sign-off in addition to Secretary authorship before it can move from `draft` to `approved`, or is Secretary approval sufficient?
