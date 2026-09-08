# Functional Requirements

## 3.1 Member Management & Onboarding
- **FR1.1** — Chairman/Secretary pre-creates a member record (name, phone, email, ID number, household) *before* the person has an account. Self-registration is not permitted — membership is always Chairman-gated.
- **FR1.2** — System generates a single-use, expiring **invite token** tied to the member's specific phone number, sent via SMS. Activation requires OTP verification of that exact number, so a leaked link cannot be used by a non-family member.
- **FR1.3** — Chairman can activate/deactivate adult members.
- **FR1.4** — A **Minor Member** record can be created directly (no invite token, no login) and linked to a Guardian via `GuardianLink`. Minors appear in the directory as "linked to [Guardian]."
- **FR1.5** — Members can be grouped into optional households/family branches.
- **FR1.6** — Directory view of all active members (adults and minors), filterable by household.

## 3.2 Contribution Rules & AGM
- **FR2.1** — Chairman can define a **`ContributionRule`** set for a campaign, specifying different amounts per member category (e.g. male: KES 2,000, female: KES 500 + one hen). Rules are data, not hardcoded logic — new categories or amounts don't require a code change.
- **FR2.2** — Contributions can be **cash or in-kind** (e.g. a hen), each with an optional estimated value for reporting purposes.
- **FR2.3** — An AGM-style contribution can be collected **incrementally across monthly meetings**, with a hard completion deadline (e.g. before the December end-year party) rather than a single lump payment.

## 3.3 Campaigns
- **FR3.1** — Chairman can create a **Fixed Contribution** campaign — amount determined by `ContributionRule`, auto-assigned to all active adult members (minors excluded by default unless the campaign explicitly opts them in).
- **FR3.2** — Chairman can create a **Pledge-Based Contribution** campaign — no fixed amount, voluntary pledges.
- **FR3.3** — Chairman can create a **Quick Emergency Contribution** — expedited flow with immediate multi-channel notification.
- **FR3.4** — Every campaign has a **`start_date`** and **`end_date`**. Progress (`total_contributed`, `remaining`, `time_remaining`) is tracked continuously and shown on the member dashboard as **aggregate figures only**.
- **FR3.5** — As the `end_date` approaches, the system sends automatic reminders: a general "campaign ending soon" notice to all members, and a personal "you still owe X" notice to members with an unfulfilled pledge.
- **FR3.6** — A campaign **auto-closes** at `end_date`; Chairman can extend the date before closure if needed. On closure, the system issues a final total-collected summary, visible to all members.
- **FR3.7** — A campaign can be linked to **one or more Events**, and an Event can draw funding from multiple campaigns (see [Data Model](data-model.md#campaign--event-many-to-many) for the join structure).

## 3.4 Events & Budgets
- **FR4.1** — Secretary can create an **Event** (e.g. End-Year Party, wedding, funeral) with a name, description, and date.
- **FR4.2** — Secretary can upload a **Budget** for an event, broken down by category (venue, food, entertainment, etc.), with a `draft → approved → published` workflow — a budget is only visible to members once approved and published.
- **FR4.3** — Members can view and download the published budget for any event.
- **FR4.4** — Every recorded **Expense** is tagged with the same event/category structure as the budget it relates to, enabling a **budget-vs-actual** comparison per category, not just a flat expense list.

## 3.5 Pledges
- **FR5.1** — Adult members can pledge an amount against a pledge-based campaign.
- **FR5.2** — A Guardian can pledge on behalf of their linked minor; Treasurer/Chairman can do so directly if no Guardian is set.
- **FR5.3** — Pledges can be fulfilled via one or more payments (partial fulfillment supported).
- **FR5.4** — Pledges can have an **installment schedule** (`PledgeInstallment`: amount + due date), including recurring monthly installments toward a single annual deadline.

## 3.6 Payments
- **FR6.1** — **v1 live methods:** bank transfer and cash, recorded manually by Treasurer/Chairman — including for a Guardian's linked child, or a minor's own pocket-money payment handed over in person.
- **FR6.2** — **In-kind payments** (e.g. a hen) are recorded with an item description, quantity, and estimated value.
- **FR6.3** — M-Pesa STK Push is implemented against the Daraja sandbox for development purposes but is **feature-flagged off** in production until the family is ready to adopt it.
- **FR6.4** — When enabled, M-Pesa payments are recorded with callback data (transaction code, amount, timestamp); callback handling is idempotent, keyed on a unique transaction code, to survive retried/duplicate deliveries.
- **FR6.5** — Manual payments always require Treasurer confirmation — there is no automated callback for bank/cash, so no auto-confirm path exists for them.
- **FR6.6** — Partial payments accumulate against a pledge/fixed contribution until fulfilled.
- **FR6.7** — Receipts are auto-generated (PDF) on confirmed payment and sent to the member, or to the Guardian for a minor's payment.

## 3.7 Expenses & Standing Welfare Fund
- **FR7.1** — Treasurer records **Expenses** against an event/category, with description, amount, date, and an optional receipt attachment.
- **FR7.2** — Expenses can optionally record a **beneficiary member** (e.g. bereavement support paid to a specific member) — useful for both welfare-fund payouts and year-end "who was supported" reporting.
- **FR7.3** — A **standing welfare/emergency fund** operates without a fixed end date — an always-open, running-balance fund (distinct from time-boxed campaigns) for bereavement, hospital emergencies, and similar needs.

## 3.8 Fines
- **FR8.1** — Chairman/Secretary can record a **fine** against a member (e.g. missed meeting, late payment), with reason, amount, and date.
- **FR8.2** — Fines appear on the member's own statement and contribute to the family's financial totals, but are not shown to other members individually (same aggregate-only privacy rule).

## 3.9 Dispute & Reconciliation
- **FR9.1** — A member can flag a payment as disputed and attach evidence (bank slip, M-Pesa message, screenshot).
- **FR9.2** — Disputed payments enter a Treasurer review queue.
- **FR9.3** — All reconciliation actions (what was disputed, resolution, who resolved it) are logged.

## 3.10 Role Handover & Leadership Terms
- **FR10.1** — Leadership positions (Chairman, Secretary, Treasurer) run on a **3-year term**. Each `HandoverRecord` captures the outgoing officer, incoming officer, term start/end, and handover reason (scheduled election vs. early resignation).
- **FR10.2** — Handover requires explicit confirmation from the outgoing officer before the incoming officer's permissions activate.
- **FR10.3** — The full leadership history (who held which role, and for which term) remains queryable indefinitely — never overwritten.

## 3.11 Meetings
- **FR11.1** — Secretary creates meetings (date, time, location, agenda).
- **FR11.2** — Secretary records attendance.
- **FR11.3** — Secretary records, edits, publishes, and locks minutes — locked minutes are preserved as immutable; any further edit creates a new version rather than altering the locked record.
- **FR11.4** — Members can view published minutes and their own attendance history.
- **FR11.5** — Secretary sends meeting notifications (in-app, SMS).

## 3.12 Reports & Transparency
- **FR12.1** — Public dashboard shows **aggregate totals only** per campaign/event (amount raised vs. target, % complete, contributor count, time remaining). Individual amounts are never shown to other members.
- **FR12.2** — Treasurer/Chairman can generate full financial reports (per campaign, per event, per period, defaulter list) with individual-level detail.
- **FR12.3** — Members can download their own contribution/payment statements (PDF); Guardians can download statements for their linked minors.
- **FR12.4** — **Annual Report**: for each `FinancialYear`, a consolidated report showing total raised (by campaign and AGM), total spent (by event and category), opening balance, closing balance, and carry-forward into the next year — presented as tables and charts on the frontend (later phase), backed by aggregation queries built now.
- **FR12.5** — Defaulter tracking with configurable reminder cadence (day 7/14/30 after due date), visible only to Treasurer/Chairman.

## 3.13 Notifications
- **FR13.1** — Multi-channel: in-app, SMS (v2: WhatsApp).
- **FR13.2** — Triggers: new campaign/event, payment confirmation, pledge reminder, campaign-ending reminder, overdue reminder, meeting notice, role change/handover, budget published.
- **FR13.3** — Notifications for a Minor Member's pledges/payments route to their Guardian's contact details.

## 3.14 Audit Trail
- **FR14.1** — All critical actions are logged immutably: pledges, payments, expenses, fines, approvals, role changes/handovers, minute edits/locks, member activation/deactivation, Guardian actions on behalf of a minor, budget approvals.
- **FR14.2** — No financial record is ever hard-deleted (soft-delete/void with reason only).
- **FR14.3** — Audit log is viewable by Chairman, exportable — the one place individual amounts and actor identities are always visible, since oversight requires it.

## 3.15 Backups & Data Retention
- **FR15.1** — Scheduled, automated database backups (at minimum daily), stored separately from the primary database.
- **FR15.2** — A documented retention and restore procedure, tested periodically — this is real family financial data and needs a recovery plan, not just a backup file sitting untested.
