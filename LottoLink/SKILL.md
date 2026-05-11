---
name: lottolink
description: >
  Use this skill for any work related to the LottoLink project — a secure digital lottery
  verification and reward automation platform built on the Paotang application for Thailand's
  Government Lottery Office (GLO). Trigger this skill whenever a team member is working on
  any LottoLink feature, module, document, spec, or task — including backend logic, fraud
  detection, ownership flows, reward processing, API design, UI/UX, database schema, test
  cases, business rules, or stakeholder documentation. Always consult this skill before
  starting any LottoLink-related work to ensure alignment with platform architecture,
  security principles, and business rules.
---

# LottoLink — Project Skill

This skill provides shared context, architecture, rules, and module ownership so the team
can work in parallel without conflicting assumptions.

---

## 1. Project Summary

LottoLink digitizes Thailand's physical lottery ecosystem through the Paotang app.

**Core value:**
- Users scan physical tickets → ownership registered digitally → rewards paid out automatically
- GLO reduces 20,000+ monthly walk-in claims and 17-min average processing time
- AI fraud detection routes suspicious claims to manual review

**Security axiom (never violate):**
> One Ticket = One Verified Owner = One Reward Claim

---

## 2. Team Modules & Ownership

Divide work by module. Each member should own one vertical end-to-end.

| Module | Scope | Key Files / Areas |
|---|---|---|
| **Ticket Verification** | QR scan, serial validation, authenticity check | `ticket-verification/` |
| **Ownership Management** | Register, lock, block duplicate ownership | `ownership/` |
| **Reward Processing** | Eligibility check, auto payout, payout routing | `reward/` |
| **Fraud Detection** | AI risk scoring, rule engine, monitoring | `fraud/` |
| **Digital Wallet** | Ticket storage, draw tracking, notifications | `wallet/` |
| **Claim Review Workflow** | Medium/high-risk routing, manual queue | `review/` |
| **Vendor Tools** | Ticket validation, inventory support for sellers | `vendor/` |
| **Operational Dashboard** | GLO metrics, alerts, audit logs | `dashboard/` |
| **Financial Integration** | PromptPay, bank linking, payout execution | `finance/` |
| **Notification System** | Draw results, reward status, ticket alerts | `notifications/` |

---

## 3. Core Data Objects

Always use these canonical shapes. Do not invent new field names.

### Ticket
```json
{
  "ticket_id": "string (encrypted)",
  "serial_number": "string",
  "qr_code": "string",
  "draw_date": "ISO date",
  "status": "unregistered | scanned | ownership_verified | reward_eligible | claimed | archived",
  "owner_account_id": "string | null",
  "ownership_locked_at": "ISO datetime | null",
  "reward_claimed": false,
  "fraud_flags": []
}
```

### Ownership Record
```json
{
  "ticket_id": "string",
  "account_id": "string",
  "registered_at": "ISO datetime",
  "device_id": "string",
  "scan_location": "lat/lng | null",
  "locked": true
}
```

### Fraud Event
```json
{
  "event_id": "string",
  "ticket_id": "string",
  "account_id": "string",
  "event_type": "duplicate_scan | duplicate_ownership | duplicate_claim | mass_scan | image_manipulation | suspicious_location",
  "risk_score": 0.0,
  "routing": "auto_payout | assisted_review | manual_investigation",
  "created_at": "ISO datetime"
}
```

### Reward Claim
```json
{
  "claim_id": "string",
  "ticket_id": "string",
  "account_id": "string",
  "amount_thb": 0,
  "payout_method": "promptpay | bank_account | paotang_wallet",
  "status": "pending | processing | completed | blocked | under_review",
  "fraud_risk_score": 0.0,
  "created_at": "ISO datetime"
}
```

---

## 4. Ticket Lifecycle (State Machine)

```
Unregistered
    ↓  [scan + authenticity pass]
Scanned
    ↓  [no existing owner → register]
Ownership Verified
    ↓  [draw date passes + ticket wins]
Reward Eligible
    ↓  [fraud score = low → auto payout]
Claimed
    ↓  [system archives]
Archived
```

**Blocked paths (system must enforce):**
- `Ownership Verified` → blocked if owner already exists → log `duplicate_ownership`
- `Claimed` → blocked if already claimed → log `duplicate_claim`
- Any state → blocked if ticket fails authenticity check

---

## 5. Fraud Risk Routing Rules

| Risk Score | Routing | Action |
|---|---|---|
| 0.0 – 0.40 | Low | Auto payout via PromptPay / bank / wallet |
| 0.41 – 0.75 | Medium | Assisted verification (staff-guided review) |
| 0.76 – 1.00 | High | Freeze payout + manual investigation + audit log |

**AI inputs for scoring:**
- Image manipulation indicators
- Counterfeit pattern match
- Scan frequency (mass scanning = flag)
- Device fingerprint anomalies
- Location clustering
- Prior fraud history on account

---

## 6. API Behavior Contracts

All modules must respect these contracts when building endpoints.

### POST /tickets/scan
- Input: `{ qr_code | serial_number, device_id, account_id }`
- Must run: authenticity → ownership check → fraud pre-screen
- Returns: `{ ticket_id, status, ownership_available: bool, fraud_flag: bool }`

### POST /tickets/register-ownership
- Input: `{ ticket_id, account_id, device_id }`
- Guard: reject if `ownership_locked = true`
- Returns: `{ success, ownership_locked_at }` or `{ error: "ownership_exists" }`

### POST /rewards/claim
- Input: `{ ticket_id, account_id, payout_method }`
- Guard: reject if `reward_claimed = true`
- Must trigger fraud scoring before any payout
- Returns: `{ claim_id, status, routing }`

### GET /tickets/:ticket_id/status
- Returns full ticket object (see §3)
- Available to consumer, GLO dashboard, audit

---

## 7. Security Rules (Non-Negotiable)

These rules apply across all modules. No exceptions.

1. **No duplicate ownership** — once `ownership_locked = true`, all further registration attempts are blocked and logged.
2. **No duplicate payouts** — once `reward_claimed = true`, all further claim attempts are blocked and logged.
3. **Fraud check is mandatory** — reward processing must never skip fraud scoring.
4. **Audit trail required** — every ownership event, claim attempt, and fraud flag must write to the audit log.
5. **Payout freeze on high risk** — high-risk claims must freeze before any financial transfer is initiated.

---

## 8. Integration Points

| System | Purpose | Notes |
|---|---|---|
| **Paotang App** | Primary consumer UI | All user flows through Paotang |
| **GLO Result Feed** | Official draw results | Trigger automatic result checking |
| **PromptPay API** | Low-risk auto payout | Primary payout method |
| **Bank API** | Linked account payout | Fallback payout method |
| **Paotang Wallet** | In-app wallet payout | Third payout option |
| **AI Fraud Model** | Risk scoring | Returns score + routing recommendation |

---

## 9. Open Business Questions (Pending Decision)

These are unresolved — do not hardcode assumptions. Flag in code with `// TODO: pending business decision`.

- **Auto-payout threshold** — what THB amount caps automatic payout?
- **Jackpot handling** — do jackpot rewards always require manual review regardless of fraud score?
- **Ownership transfer** — can a registered owner transfer a ticket to another account?
- **Vendor role** — can vendors confirm or co-sign ownership at point of sale?

---

## 10. Reference Documents

| Document | Location | Purpose |
|---|---|---|
| Full Product Spec | `references/lottolink-spec.md` | Complete feature and flow definitions |
| User Flow Diagrams | `references/user-flows.md` | Step-by-step flow per actor |
| Fraud Model Spec | `references/fraud-model.md` | AI inputs, scoring, routing logic |
| DB Schema | `references/schema.sql` | Canonical table definitions |
| API Contract | `references/api-contracts.md` | Full endpoint specs |

> Read the relevant reference file before working on your module.

---

## 11. Parallel Work Guidelines

- **Own your module** — one person per module (see §2 table)
- **Never modify shared data contracts** (§3) without team alignment
- **State machine transitions** (§4) are shared — changes require review from all
- **Security rules** (§7) cannot be relaxed by any single module
- **API contracts** (§6) are the interface between modules — treat as stable
- **Tag unresolved items** with `// TODO: pending business decision` (see §9)
- **Commit naming**: use prefix matching module name, e.g. `fraud: add mass-scan detection rule`
