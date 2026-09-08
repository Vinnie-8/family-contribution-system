# Family Contribution Management System

## Project Overview

### Vision
Digitize how families manage meetings, contributions, campaigns, events, and payments — replacing manual record-keeping and WhatsApp chaos with a single, transparent, auditable source of truth, while preserving traditional family governance roles (Chairman, Secretary, Treasurer).

### Problem Statement
Families managing shared finances (AGM dues, emergency support, events, projects) currently rely on manual tracking (notebooks, spreadsheets, WhatsApp), leading to:

- Disputes over who paid/pledged what
- Lost or forgotten pledges
- No visibility into overdue members
- Difficulty including elderly/phoneless members and children
- No historical audit trail for accountability
- No connection between money raised and money actually spent on events

### Goals
- Provide transparency (aggregate) without exposing individual contribution amounts between members
- Support fixed (AGM), voluntary (pledge-based), and standing (welfare fund) contributions
- Track payments manually first (bank transfer/cash), with M-Pesa STK Push built but gated for future activation
- Model real family financial life: events, budgets, expenses, fines, and beneficiary support — not just money coming in
- Maintain a permanent, tamper-proof audit trail
- Work for members without smartphones, and for minors without phone numbers at all

!!! info "Scope for v1"
    **Target:** one family (~60 members), web app only, manual payment recording, single financial year at a time.

    **Built now, designed for later:** multi-tenancy (schema is tenant-scoped from day one, no onboarding UI yet) and M-Pesa STK Push (built against the Daraja sandbox, feature-flagged off until the family adopts it).

    **Out of scope for v1:** multi-currency, family asset/property management, WhatsApp Business API, native mobile app, multi-family self-serve signup.

### How This Documentation Is Organized
- **[Roles & Permissions](roles-and-permissions.md)** — who can do what
- **[Functional Requirements](functional-requirements.md)** — what the system must do, module by module
- **[Non-Functional Requirements](non-functional-requirements.md)** — performance, security, and quality constraints
- **[Data Model](data-model.md)** — entities, relationships, and the reasoning behind each
- **[Security & Privacy](security-and-privacy.md)** — how sensitive flows (onboarding, minors, payments, privacy) are handled safely
- **[Build Plan](build-plan.md)** — tech stack and branch-by-branch delivery order
- **[Decision Log](decision-log.md)** — resolved and open design questions, with rationale
