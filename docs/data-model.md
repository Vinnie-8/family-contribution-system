# Data Model

## Entity-Relationship Diagram

```mermaid
erDiagram
    FAMILY ||--o{ HOUSEHOLD : has
    FAMILY ||--o{ MEMBER : has
    FAMILY ||--o{ CAMPAIGN : has
    FAMILY ||--o{ EVENT : has
    FAMILY ||--o{ MEETING : has
    FAMILY ||--o{ STANDING_FUND : has
    FAMILY ||--o{ FINANCIAL_YEAR : has
    FAMILY ||--o{ AUDIT_LOG : has

    HOUSEHOLD ||--o{ MEMBER : groups

    MEMBER ||--o{ USER_ROLE : holds
    MEMBER ||--o{ GUARDIAN_LINK : "is guardian in"
    MEMBER ||--o| GUARDIAN_LINK : "is minor in"
    MEMBER ||--o{ INVITE_TOKEN : "verifies via"
    MEMBER ||--o{ PLEDGE : makes
    MEMBER ||--o{ PAYMENT : makes
    MEMBER ||--o{ FINE : "fined against"
    MEMBER ||--o{ EXPENSE : "benefits from"
    MEMBER ||--o{ ATTENDANCE : attends
    MEMBER ||--o{ HANDOVER_RECORD : "outgoing/incoming in"

    CAMPAIGN ||--o{ CONTRIBUTION_RULE : defines
    CAMPAIGN ||--o{ PLEDGE : receives
    CAMPAIGN }o--o{ EVENT : funds

    EVENT ||--o{ BUDGET : has
    EVENT ||--o{ EXPENSE : incurs

    PLEDGE ||--o{ PLEDGE_INSTALLMENT : "scheduled as"
    PLEDGE ||--o{ PAYMENT : "fulfilled by"

    PAYMENT ||--o| DISPUTE : "may be disputed as"

    STANDING_FUND ||--o{ PAYMENT : receives
    STANDING_FUND ||--o{ EXPENSE : disburses

    MEETING ||--o{ ATTENDANCE : records
    MEETING ||--o{ MINUTES_RECORD : has

    FINANCIAL_YEAR ||--o{ CAMPAIGN : bounds
    FINANCIAL_YEAR ||--o{ EXPENSE : bounds

    FAMILY {
        uuid id PK
        string name
        datetime created_at
    }
    MEMBER {
        uuid id PK
        uuid family_id FK
        uuid household_id FK
        string name
        string phone "nullable for minors"
        string email "nullable for minors"
        string category "male, female, etc"
        bool is_minor
        bool is_active
    }
    GUARDIAN_LINK {
        uuid id PK
        uuid guardian_member_id FK
        uuid minor_member_id FK
        string relationship
    }
    CAMPAIGN {
        uuid id PK
        uuid family_id FK
        uuid financial_year_id FK
        string type "fixed, pledge, emergency, standing"
        date start_date
        date end_date
        decimal target_amount
        bool includes_minors
        string status
    }
    CONTRIBUTION_RULE {
        uuid id PK
        uuid campaign_id FK
        string member_category
        decimal cash_amount
        string in_kind_item
    }
    EVENT {
        uuid id PK
        uuid family_id FK
        string name
        date event_date
    }
    BUDGET {
        uuid id PK
        uuid event_id FK
        string category
        decimal planned_amount
        string status "draft, approved, published"
        uuid uploaded_by FK
    }
    PLEDGE {
        uuid id PK
        uuid member_id FK
        uuid campaign_id FK
        decimal amount
    }
    PLEDGE_INSTALLMENT {
        uuid id PK
        uuid pledge_id FK
        decimal amount
        date due_date
        string status
    }
    PAYMENT {
        uuid id PK
        uuid pledge_id FK
        uuid standing_fund_id FK
        uuid member_id FK
        string method "bank_transfer, cash, mpesa, in_kind"
        decimal amount
        string item_description "for in_kind"
        string mpesa_transaction_code "nullable, unique"
        string status
        uuid confirmed_by FK
    }
    EXPENSE {
        uuid id PK
        uuid family_id FK
        uuid event_id FK
        uuid standing_fund_id FK
        uuid beneficiary_member_id FK
        string category
        decimal amount
        date date
        uuid recorded_by FK
        uuid approved_by FK
    }
    FINE {
        uuid id PK
        uuid member_id FK
        string reason
        decimal amount
        date date
    }
    MEETING {
        uuid id PK
        uuid family_id FK
        date meeting_date
        string location
        string agenda
    }
    HANDOVER_RECORD {
        uuid id PK
        string role
        uuid outgoing_member_id FK
        uuid incoming_member_id FK
        date term_start
        date term_end
        string status
    }
    FINANCIAL_YEAR {
        uuid id PK
        uuid family_id FK
        int year
        decimal opening_balance
        decimal closing_balance
    }
```

## Notes on Key Design Choices

### Campaign ↔ Event: many-to-many
A **Campaign** raises money; an **Event** is what the money is for. The family's own example — monthly AGM contributions funding the End-Year Party — already shows they aren't one-to-one: one event can be funded by several campaigns, and (less commonly) a single broad campaign could contribute toward more than one event. This is modeled as a join relationship (`CAMPAIGN }o--o{ EVENT`) rather than a foreign key on either side.

### Minors are `Member` rows, not a separate table
A minor is a `Member` with `is_minor = true` and `phone`/`email` left null. This keeps every relationship that already points at `Member` — `Pledge`, `Payment`, `Attendance`, `Fine` — working unchanged for minors, rather than needing parallel tables and parallel foreign keys everywhere. `GUARDIAN_LINK` is the only place the guardian/minor relationship is explicit.

### `ContributionRule` instead of hardcoded amounts
The family's AGM structure (different cash/in-kind amounts by category) is stored as **data attached to a campaign**, not application logic. Changing an amount, or adding a new category, is a data change — not a code deployment.

### `PledgeInstallment` as its own entity
Recurring monthly AGM collection toward one annual deadline needs more than a single amount+date on `Pledge` — each installment has its own due date and fulfillment status, so it's modeled as a child table.

### `StandingFund` is structurally different from `Campaign`
A welfare/emergency fund has no `end_date` and no target — it's an always-open running balance. Rather than forcing it into `Campaign`'s time-boxed shape, it gets its own light entity that `Payment` and `Expense` can both reference.

### `Payment.method` includes `in_kind`
A hen is not cash. Payments carry a `method` and, for `in_kind`, an `item_description` and estimated value — this keeps a single payment table instead of splitting cash and in-kind contributions into separate flows.

### `FinancialYear` as the reporting boundary
Annual reports, opening/closing balances, and year-over-year comparisons all need a clean period boundary. `Campaign` and `Expense` both reference the `FinancialYear` they belong to, so an annual report is a filtered aggregation query, not a bespoke calculation.

### `Document` (not shown above, generic attachment)
A reusable polymorphic attachment table (`entity_type`, `entity_id`, `file_url`, `uploaded_by`) backs published budgets, expense receipts, dispute evidence, and minutes attachments — one mechanism instead of four.
