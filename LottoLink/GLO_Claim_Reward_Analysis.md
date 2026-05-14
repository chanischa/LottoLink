# GLO LottoLink — Claim Reward Process Analysis
> Hackathon project analysis for Government Lottery Office (GLO) Thailand
> Focus: Reward claim automation challenge — physical vs digital channel

---

## Table of Contents
1. [The Core Problem](#1-the-core-problem)
2. [Pain Point Map](#2-pain-point-map)
3. [5 Why Analysis](#3-5-why-analysis)
4. [User Flow — Two Channels](#4-user-flow--two-channels)
5. [User Stories](#5-user-stories)
6. [Security Principle](#6-security-principle)

---

## 1. The Core Problem

### Why must physical ticket buyers walk in to GLO or visit an agent to claim?

Because the entire claim process for physical tickets was built around the assumption that **the ticket itself is the only proof of ownership.**

| Reason | Explanation |
|---|---|
| Ticket is bearer property | Whoever holds the paper is treated as the owner — anonymous by design, like cash |
| Identity only established at claim | No name or account is attached at purchase, so presence is the only verification |
| Verification requires human judgement | Staff visually inspect serial numbers and security markings — no system record to check |
| No pre-draw ownership lock | Without a record of "ticket X belongs to account Y", nothing can be automated |
| Structural, not a policy choice | The walk-in requirement exists because the system has nothing else to act on |

> **The gap LottoLink closes:** The moment a buyer scans their ticket and registers it to their account, the system has the ownership record it needs — and the automated claim path becomes available.

---

## 2. Pain Point Map

### Consumer — Physical Ticket Buyer

| Pain Point | Description | Impact |
|---|---|---|
| Walk-in only to claim | Must visit GLO office or find an agent — no digital claim path exists | High |
| Lost ticket = lost prize | No digital backup, no ownership proof if paper is lost or damaged | High |
| Manual result checking | Must check TV, newspaper, or website manually — easy to miss | Medium |

### Channel Gap — Same Draw, Unequal Claim Experience

| Pain Point | Description | Impact |
|---|---|---|
| Two-tier experience | Paotang buyers claim digitally and automatically · physical buyers cannot | High |
| No pre-claim ownership proof | Physical ticket identity is unverifiable before the draw | High |

### GLO — Claim Operations

| Pain Point | Description | Impact |
|---|---|---|
| 20,000+ claims/month | Approximately 17 min average processing time per claim | High |
| 100% manual verification | Every claim requires staff · cannot scale | High |
| Fraud hard to detect | Duplicate claims and counterfeit tickets difficult to catch at volume | High |

### Shared Root Cause

> **Physical tickets have no digital ownership layer — claim verification cannot be automated without it.**

### Automation Blockers
- Claim bottleneck
- Fraud exposure
- Unequal channel access
- No pre-registered ownership data

---

## 3. Five Why Analysis

### Problem A — Physical Buyer Walk-In Claim

| Step | Statement |
|---|---|
| Problem | Physical buyers must visit GLO or an agent in person to claim prizes |
| Why 1 | No digital ownership record exists for physical tickets |
| Why 2 | Ticket is not linked to the buyer at point of purchase |
| Why 3 | No scan-to-own step exists in the purchase flow |
| Why 4 | Physical and digital channels are not integrated |
| Why 5 | No digital identity layer was ever built for paper tickets |
| **Root Cause A** | **No digital identity layer for paper tickets** |

---

### Problem B — GLO Operational Overload

| Step | Statement |
|---|---|
| Problem | Staff overwhelmed by 20,000+ claims per month at 17 min each |
| Why 1 | Every claim is processed manually with no automated eligibility check |
| Why 2 | No real-time result matching to registered ticket holders |
| Why 3 | Tickets have no pre-registered owner before draw date |
| Why 4 | Verification is entirely decoupled from ticket registration |
| Why 5 | Claim process was never designed to be automatable |
| **Root Cause B** | **Claim process cannot automate without pre-draw ownership data** |

---

### Problem C — Fraud and Duplicate Claims

| Step | Statement |
|---|---|
| Problem | Fraudulent and duplicate claims are hard to detect at scale |
| Why 1 | No ticket-to-owner binding before result announcement |
| Why 2 | Physical tickets are anonymous until the moment of claim |
| Why 3 | No pre-draw registration system for ownership exists |
| Why 4 | Fraud is only detectable reactively, never proactively |
| Why 5 | System has no structural mechanism to prevent fraud before the draw |
| **Root Cause C** | **Ownership is unverifiable before the draw** |

---

### Converged System Root Cause

> **Physical tickets carry no pre-draw digital ownership — making automated claim verification structurally impossible under the current system.**

---

## 4. User Flow — Two Channels

### Channel A — Paotang App Buyer

```
Ticket in digital wallet
(Ownership auto-registered at purchase)
        ↓
Push notification sent
(Draw day reminder)
        ↓
Auto result check
(System matches all wallets instantly)
        ↓
Win notification received
(Prize amount shown in app)
        ↓
AI fraud score generated
        ↓
[Low risk]    → Auto payout to PromptPay / wallet / bank
[Medium risk] → Additional verification step
[High risk]   → GLO manual investigation
        ↓
✅ Prize in wallet — no travel, fully digital
```

---

### Channel B — Physical Ticket Buyer

```
Paper ticket stored at home
(No digital record · no reminder)
        ↓
Must remember draw date
(No notification sent)
        ↓
Check result manually
(TV · newspaper · website)
        ↓
Won — must claim in person
(Physical presence required)
        ↓
    ┌─────────────────────────┐
    ▼                         ▼
Walk in to GLO           Go to agent
Queue · ~17 min          Agent verifies paper
Manual verification      Agent pays out cash
    └─────────────────────────┘
                ↓
    Manual paper verification
    (Staff checks ticket by hand)
                ↓
    ❌ Cash received in person — travel required both paths
```

---

### The Gap

> **Identical lottery. Identical draw. Completely different claim experience.**
>
> Channel A: automatic, digital, no travel required.
> Channel B: manual, physical, travel required every single time.

---

## 5. User Stories

### Consumer — Channel A · Paotang Digital Buyer

---

#### US-C01 — Automatic prize detection and notification `Priority: P1`

> **As a** Paotang lottery buyer,
> **I want** the app to automatically check if my ticket won,
> **So that** I never have to manually look up results or miss a prize.

**Acceptance Criteria:**
- [ ] System auto-matches all registered tickets against GLO results on draw day
- [ ] User receives a push notification with win/no-win result within 1 hour of announcement
- [ ] Notification shows the prize amount if applicable
- [ ] No action required from the user to trigger the check

**Tags:** `result-check` `notification` `automation`

---

#### US-C02 — Automatic prize payout `Priority: P1`

> **As a** Paotang lottery winner,
> **I want** my prize transferred automatically to my PromptPay or bank account,
> **So that** I don't have to travel anywhere or take any manual steps to claim.

**Acceptance Criteria:**
- [ ] Low-risk prizes are paid out automatically within 24 hours of draw announcement
- [ ] Transfer is made to the user's linked PromptPay, Paotang wallet, or bank account
- [ ] User receives a transfer confirmation notification with amount and reference
- [ ] Payout is permanently blocked if the ticket has already been claimed
- [ ] Full claim record is stored in the user's history

**Tags:** `payout` `PromptPay` `automation`

---

### Consumer — Channel B · Physical Ticket Buyer

---

#### US-C03 — Scan physical ticket to register ownership `Priority: P1`

> **As a** physical lottery ticket buyer,
> **I want to** scan my ticket in Paotang to register ownership,
> **So that** I can claim my prize digitally without having to travel to GLO or an agent.

**Acceptance Criteria:**
- [ ] Ticket scan completes in under 3 seconds via QR code or manual serial entry
- [ ] Ownership is locked to the user's account immediately after verification
- [ ] Duplicate scan on an already-owned ticket returns a clear blocked error
- [ ] Ticket appears instantly in the digital lottery wallet with draw date
- [ ] Scan timestamp and device are recorded for audit

**Tags:** `scan` `ownership` `wallet`

---

#### US-C04 — Claim prize digitally after scanning physical ticket `Priority: P1`

> **As a** physical ticket owner who registered via scan,
> **I want to** receive my prize through Paotang,
> **So that** I get the same digital claim experience as app buyers — no walk-in required.

**Acceptance Criteria:**
- [ ] Once ownership is registered, system auto-checks result on draw day
- [ ] Winning ticket triggers the same AI fraud scoring and payout flow as Channel A
- [ ] Low-risk prize is paid out automatically to PromptPay or linked account
- [ ] User receives push notification confirming payout
- [ ] Physical walk-in is no longer required for registered tickets

**Tags:** `claim` `payout` `channel-parity`

---

#### US-C05 — Draw result notification for physical ticket owners `Priority: P2`

> **As a** physical ticket owner who scanned their ticket,
> **I want to** receive an automatic result notification on draw day,
> **So that** I never miss checking my result the way I might with a paper ticket.

**Acceptance Criteria:**
- [ ] Push notification sent within 1 hour of GLO draw announcement
- [ ] Notification shows win/no-win result and prize amount if applicable
- [ ] Reminder notification sent 1 day before the draw

**Tags:** `notification` `result`

---

### GLO — Claim Operations and System

---

#### US-G01 — Automated claim eligibility verification `Priority: P1`

> **As the** GLO system,
> **I need to** verify prize eligibility automatically against registered ownership,
> **So that** staff no longer have to manually process every walk-in claim.

**Acceptance Criteria:**
- [ ] System cross-references draw result against all registered ticket owners automatically
- [ ] Eligible claims are flagged and routed for payout without staff intervention
- [ ] Manual review queue contains only medium and high-risk cases
- [ ] Processing time for low-risk claims reduces from ~17 min to under 24 hours automated

**Tags:** `automation` `eligibility` `GLO-ops`

---

#### US-G02 — Duplicate payout prevention `Priority: P1`

> **As the** GLO system,
> **I need to** ensure every winning ticket pays out exactly once,
> **So that** duplicate and fraudulent claims are structurally blocked, not just monitored.

**Acceptance Criteria:**
- [ ] System checks full claim history before processing any payout
- [ ] A second claim on an already-paid ticket is immediately rejected
- [ ] Rejected attempt is logged and escalated to fraud queue automatically
- [ ] One ticket is permanently bound to one owner and one payout record

**Tags:** `deduplication` `fraud` `security`

---

#### US-G03 — Real-time fraud monitoring for claims `Priority: P1`

> **As a** GLO fraud officer,
> **I want to** see all high-risk claims flagged in a live dashboard,
> **So that** I can investigate and block suspicious payouts before money is transferred.

**Acceptance Criteria:**
- [ ] Dashboard shows all claims with AI risk score, flag reason, and status
- [ ] Officer can approve, reject, or escalate any claim with one action
- [ ] All officer actions are logged with timestamp and ID for audit
- [ ] High-risk claim triggers an automatic payout freeze pending review

**Tags:** `fraud` `dashboard` `AI-scoring`

---

#### US-G04 — Claim operations metrics dashboard `Priority: P2`

> **As a** GLO operations manager,
> **I want** live metrics on claim volume, processing time, and automation rate,
> **So that** I can measure how much the system has reduced manual workload.

**Acceptance Criteria:**
- [ ] Dashboard shows daily auto-processed vs manual review claim split
- [ ] Average processing time tracked and benchmarked against the 17-min baseline
- [ ] Fraud alert rate and resolution rate shown as trend graphs
- [ ] Data exportable as CSV for reporting

**Tags:** `metrics` `reporting` `operations`

---

## 6. Security Principle

```
One Ticket = One Verified Owner = One Reward Claim
```

Once a ticket has verified ownership or a completed reward payout, the system permanently blocks:
- Duplicate ownership registration
- Repeated reward claims

### Fraud Prevention Flow

```
Ticket Scan
      ↓
Authenticity Verification
      ↓
Ownership Validation (pre-draw lock)
      ↓
Draw Result Matching
      ↓
AI Fraud Risk Score
      ↓
[Low risk]    → Auto reward transfer
[Medium risk] → Assisted verification
[High risk]   → Manual investigation + payout freeze
```

### Ticket Lifecycle

```
Unregistered
      ↓
Scanned → Ownership locked to account
      ↓
Draw day → Result matched automatically
      ↓
Prize eligible → AI fraud scored
      ↓
Reward claimed → Payout processed
      ↓
Archived
```

---

*Document generated from LottoLink hackathon analysis session*
*GLO Automation System Challenge — Claim Reward Process Focus*
*Platform: Paotang Application*
