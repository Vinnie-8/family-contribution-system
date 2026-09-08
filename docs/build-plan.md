# Build Plan

## Tech Stack

| Layer | Choice | Why |
|---|---|---|
| Backend | FastAPI (Python) | Fast to build, async-friendly for future M-Pesa callback handling |
| Frontend | Next.js (React) | Consistent dev experience; built after the backend/database is complete |
| Database | PostgreSQL | Strong support for financial data integrity, transactions, constraints |
| Cache/Queue | Redis | Background jobs for reminders, notification queue, incremental aggregate caching |
| Payments | Manual (bank/cash) live in v1; Safaricom Daraja API (STK Push) built but feature-flagged off | Family currently uses bank transfer; M-Pesa work supports later adoption and learning |
| SMS | Africa's Talking or similar | For non-app-first and elderly members |
| PDF generation | WeasyPrint or similar | Receipts, statements, published budgets, annual reports |
| Migrations | Alembic | Versioned, reversible schema history from the first commit |
| Infra | Docker & Docker Compose | Local/deployment parity |

## Branch-by-Branch Delivery Order

1. `feature/auth` — registration via invite token + OTP, login, roles, **plus the audit-log utility/middleware** so every later module logs into it as it's built
2. `feature/members` — profiles, households, Guardian ↔ Minor links
3. `feature/campaigns` — fixed & pledge-based campaigns, `ContributionRule`, start/end dates, auto-close, reminders
4. `feature/events-budgets` — events, budget upload/approval workflow, Campaign ↔ Event linking
5. `feature/pledges` — pledge creation, installment schedules
6. `feature/payments` — manual bank/cash/in-kind recording first; M-Pesa STK Push built against Daraja sandbox but feature-flagged off
7. `feature/expenses-fines-fund` — expenses (with beneficiary tracking), fines, standing welfare fund
8. `feature/disputes` — dispute flagging & reconciliation
9. `feature/meetings` — meetings, attendance, minutes with versioned locking
10. `feature/reports` — aggregate-only public dashboard, Treasurer full reports, personal statements, **annual report per `FinancialYear`**
11. `feature/notifications` — in-app + SMS, with Guardian-routing for minors
12. `feature/handover` — leadership term / election handover flow
13. `feature/audit-log` — hardening pass only; core logging already exists from module 1 onward

!!! note "Frontend sequencing"
    Per current plan, backend and database are built and stabilized first across all modules above. Frontend (Next.js) work — including the tables/charts for annual reports — begins once the backend API surface is complete and stable.
