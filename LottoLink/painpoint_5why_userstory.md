# GLO LottoLink — Automation System Analysis
> Hackathon project analysis for Government Lottery Office (GLO) Thailand
> Platform: Paotang Application

---

## Table of Contents
1. [Project Overview](#1-project-overview)
2. [Current State — Two Channel Problem](#2-current-state--two-channel-problem)
3. [Pain Point Map](#3-pain-point-map)
4. [5 Why Analysis](#4-5-why-analysis)
5. [User Flow](#5-user-flow)
6. [User Stories](#6-user-stories)

---

## 1. Project Overview

**LottoLink** is a secure digital lottery verification and automated reward claim platform designed to modernise Thailand's lottery ecosystem through the Paotang application.

### Core Problem
- GLO handles **20,000+ walk-in reward claims per month**
- Average processing time: **~17 minutes per queue**
- All verification is **manual and paper-based**
- Physical ticket buyers have **no digital claim path**

### Core Objectives
| Objective | Description |
|---|---|
| Operational Automation | Reduce manual reward processing workload at GLO |
| Fraud Prevention | Block counterfeit tickets, duplicate ownership, duplicate reward claims |
| Public Convenience | Improve reward claim speed, ticket management, payout accessibility |
| Digital Transformation | Support Thailand's Smart Government and cashless infrastructure goals |

---

## 2. Current State — Two Channel Problem

### Channel A — Buy on Paotang App
```
Consumer buys on Paotang
        ↓
Ticket stored in app wallet
(Ownership auto-registered)
        ↓
App auto-checks result
(Push notification on draw day)
        ↓
Claim reward in-app
(Transfer to bank / e-wallet)
        ↓
✅ Done — fully digital, no travel
```

### Channel B — Buy Physical Ticket from Agent
```
Consumer buys from agent
(Physical paper ticket, cash)
        ↓
Keep paper ticket safely
(No digital record exists)
        ↓
Check result manually
(TV, newspaper, or website)
        ↓
Won a prize — how to claim?
        ↓
    ┌───────────────────────┐
    ▼                       ▼
Walk in to GLO         Claim via agent
Queue · ~17 min        Agent verifies paper
Manual verification    Agent pays out cash
    └───────────────────────┘
                ↓
    Cash received — process ends
```

> ⚠️ **The gap:** Both claim methods in Channel B require physical presence. Consumer must travel to GLO or find an agent — no digital option exists for paper ticket holders. Same lottery. Same draw. Completely different experience.

---

## 3. Pain Point Map

### Consumer — Physical Ticket Buyer
| Pain Point | Description |
|---|---|
| No digital claim path | Must visit GLO office or find an agent in person |
| Ticket loss = prize loss | No digital backup; lost ticket means forfeited prize |
| Manual result checking | Easy to forget or miss; no automatic notification |

### Channel Gap — Physical vs Digital Buyers
| Pain Point | Description |
|---|---|
| Two-tier experience, same lottery | Paotang buyers get full digital service; physical buyers get none |
| No ownership proof for paper tickets | Identity is unverifiable until moment of claim |

### Vendor — Lottery Agent
| Pain Point | Description |
|---|---|
| Must travel to collect stock | No digital ordering system for ticket allocation |
| Paper-based inventory | No real-time stock visibility or management |
| Counterfeit claim risk | No way to verify ticket ownership when paying out prizes |

### GLO — Operations
| Pain Point | Description |
|---|---|
| 20,000+ walk-ins/month | Approximately 17 min avg processing time per claim |
| 100% manual verification | Staff-heavy process, impossible to scale |
| Fraud hard to detect | Duplicate claims and counterfeits difficult to catch at volume |

### Shared Root Cause
> **No unified digital identity or ownership layer linking tickets, people, and outcomes across the entire lottery ecosystem.**

---

## 4. Five Why Analysis

### Problem A — Physical Claim Walk-In

| Step | Statement |
|---|---|
| Problem | Physical buyers must visit GLO or agent to claim prizes |
| Why 1 | No digital ownership record for physical tickets |
| Why 2 | Ticket is not linked to buyer at point of purchase |
| Why 3 | No scan-to-own mechanism exists in the purchase flow |
| Why 4 | Physical and digital channels are not integrated |
| Why 5 | No digital identity layer was ever built for paper tickets |
| **Root Cause A** | **No digital identity layer for paper tickets** |

---

### Problem B — Vendor Distribution Travel

| Step | Statement |
|---|---|
| Problem | Vendors travel to GLO headquarters to collect ticket batches |
| Why 1 | No digital ordering system for ticket allocation |
| Why 2 | GLO uses paper-based distribution workflows |
| Why 3 | No authenticated vendor digital identity platform exists |
| Why 4 | Legacy infrastructure is not connected to digital applications |
| Why 5 | No investment in digital distribution channel has been made |
| **Root Cause B** | **No digital vendor identity or distribution channel** |

---

### Problem C — GLO Operational Overload

| Step | Statement |
|---|---|
| Problem | Staff overwhelmed by 20,000+ claims/month at 17 min each |
| Why 1 | Every claim is processed manually with no automation |
| Why 2 | No real-time result matching to registered ticket holders |
| Why 3 | Tickets have no pre-registered owner before draw date |
| Why 4 | Verification is entirely decoupled from ticket registration |
| Why 5 | Claim process was never designed to be automatable |
| **Root Cause C** | **Claim process not automatable without pre-draw ownership data** |

---

### Problem D — Fraud and Duplicate Claims

| Step | Statement |
|---|---|
| Problem | Fraudulent and duplicate claims are hard to detect at scale |
| Why 1 | No ticket-to-owner binding exists before result announcement |
| Why 2 | Physical tickets are anonymous until the moment of claim |
| Why 3 | No pre-draw registration system for ownership |
| Why 4 | Fraud is only detectable reactively, not proactively |
| Why 5 | System has no structural mechanism to prevent fraud pre-draw |
| **Root Cause D** | **Ownership is unverifiable before the draw** |

---

### Converged System Root Cause

> **GLO has no unified digital ownership, identity, or distribution infrastructure — making automation, fraud prevention, and equal access structurally impossible under the current system.**

---

## 5. User Flow

### Actors
- **Vendor** — lottery agent
- **Consumer** — ticket buyer (physical channel)
- **GLO / System** — Government Lottery Office and LottoLink platform

---

### Stage 1 — Ticket Acquisition
```
Vendor                    Consumer                  GLO / System
   │                          │                          │
   ├── Order tickets via app ──►──────────────────────── ► Fulfil ticket allocation
   │   (No travel required)   │                          │  Assign serial batch
   │                          │                          │
   │ ◄── Batch confirmed ──── │ ◄─── Buy ticket ──────── │
   │                          │      Physical ticket      │
   │                          │      in hand              │
```

### Stage 2 — Scan & Register Ownership
```
Consumer                              GLO / System
    │                                      │
    ├── Open Paotang → LottoLink ──────────►── Verify authenticity
    │   Scan QR or serial number           │   Check serial + duplicate
    │                                      │
```

### Stage 3 — Ownership Decision
```
GLO / System
    │
    ├── No existing owner?
    │       ↓ Yes
    │   Lock ownership to user account ──────► Consumer: Ticket added to wallet
    │                                                     Digital ownership confirmed
    │       ↓ Already owned
    │   Block ownership request
    │   Log fraud event
```

### Stage 4 — Draw & Result Matching
```
GLO / System                              Consumer
    │                                         │
    ├── GLO announces results                 │
    │   Auto-match all registered wallets ────►── Push notification sent
    │                                         │   "You won / no win"
```

### Stage 5 — Fraud Risk Analysis
```
GLO / System
    │
    ├── AI risk score generated
    │
    ├── [Low risk]    ──────────────────► Auto payout
    ├── [Medium risk] ──────────────────► Assisted verification
    └── [High risk]   ──────────────────► Manual investigation
```

### Stage 6 — Reward Payout
```
Consumer / System
    │
    ├── Transfer via PromptPay / Paotang wallet / bank
    │
    └── Payout logged & archived
        Audit trail complete
        │
        ▼
    Ticket archived — lifecycle complete
    One ticket · One owner · One payout
```

---

## 6. User Stories

### Vendor Stories

#### US-V01 — Digital Ticket Ordering `Priority: P1`
> **As a** lottery vendor,
> **I want to** request ticket batches through the Paotang app,
> **So that** I don't have to travel to GLO headquarters to collect my stock.

**Acceptance Criteria:**
- [ ] Vendor can submit a batch order specifying quantity and draw date
- [ ] System confirms order and assigns serial range to vendor account
- [ ] Vendor receives notification when batch is ready for collection or delivery
- [ ] Order history is accessible in the vendor dashboard

---

#### US-V02 — Digital Inventory Management `Priority: P1`
> **As a** lottery vendor,
> **I want to** see my current ticket inventory and track unsold stock digitally,
> **So that** I can manage returns and avoid financial loss.

**Acceptance Criteria:**
- [ ] Vendor dashboard shows total allocated, sold, and unsold ticket counts
- [ ] Each ticket's status (unregistered, owned, claimed) is visible
- [ ] System flags tickets approaching draw date that are still unsold

---

#### US-V03 — Point-of-Sale Ticket Validation `Priority: P2`
> **As a** lottery vendor,
> **I want to** verify a ticket's authenticity before handing it to a customer,
> **So that** I am protected from unknowingly selling counterfeit tickets.

**Acceptance Criteria:**
- [ ] Vendor can scan any ticket QR to confirm it belongs to their batch
- [ ] System shows pass/fail authenticity result in under 2 seconds
- [ ] Invalid tickets are flagged and logged for GLO review

---

### Consumer Stories

#### US-C01 — Scan to Register Ownership `Priority: P1`
> **As a** lottery buyer,
> **I want to** scan my physical ticket in Paotang,
> **So that** I have a digital ownership record and won't lose my prize if I lose the paper ticket.

**Acceptance Criteria:**
- [ ] Ticket scan completes in under 3 seconds via QR or serial entry
- [ ] Ownership is locked to the user's account immediately after verification
- [ ] Duplicate scan attempt returns a "ticket already registered" error
- [ ] Ticket appears in the user's digital lottery wallet

---

#### US-C02 — Automatic Prize Payout `Priority: P1`
> **As a** lottery winner,
> **I want my** prize transferred automatically to my PromptPay or Paotang wallet,
> **So that** I don't have to travel to GLO or an agent to claim it.

**Acceptance Criteria:**
- [ ] System auto-matches registered tickets to draw results after announcement
- [ ] Low-risk winning tickets are paid out within 24 hours automatically
- [ ] User receives push notification with prize amount and transfer status
- [ ] Payout is blocked if the ticket has already been claimed

---

#### US-C03 — Digital Lottery Wallet `Priority: P2`
> **As a** regular lottery buyer,
> **I want to** see all my registered tickets, draw dates, and results in one place,
> **So that** I never forget to check if I've won.

**Acceptance Criteria:**
- [ ] Wallet shows all active tickets with draw date and countdown
- [ ] Past tickets show result (win/no win) and payout status
- [ ] User gets a push notification reminder 1 day before each draw

---

### GLO / System Stories

#### US-G01 — Real-Time Fraud Monitoring `Priority: P1`
> **As a** GLO fraud officer,
> **I want to** see flagged claims and suspicious scan patterns in a dashboard,
> **So that** I can investigate and block fraudulent payouts before they occur.

**Acceptance Criteria:**
- [ ] Dashboard shows all high-risk claims with AI fraud score and reason
- [ ] Officer can approve, reject, or escalate any flagged claim
- [ ] All actions are logged with timestamp and officer ID
- [ ] Mass scanning patterns from a single device trigger an automatic alert

---

#### US-G02 — Duplicate Payout Prevention `Priority: P1`
> **As the** GLO system,
> **I need to** ensure that each winning ticket is paid out exactly once,
> **So that** duplicate or fraudulent reward claims are structurally impossible.

**Acceptance Criteria:**
- [ ] System checks claim history before processing any payout
- [ ] A second claim attempt on a paid ticket is rejected with an error log
- [ ] One ticket is linked to exactly one owner and one payout record

---

#### US-G03 — Operational Metrics Dashboard `Priority: P2`
> **As a** GLO operations manager,
> **I want** live metrics on claim volume, payout status, and fraud alerts,
> **So that** I can measure automation impact and make staffing decisions.

**Acceptance Criteria:**
- [ ] Dashboard shows daily claim volume vs manual review count
- [ ] Payout processing time is tracked and benchmarked against the 17-min baseline
- [ ] Fraud alert rate and resolution rate are visible with trend graphs

---

## Security Principle

```
One Ticket = One Verified Owner = One Reward Claim
```

Once a ticket has verified ownership OR completed reward payout, the system permanently blocks:
- Duplicate ownership registration
- Repeated reward claims

---

## Fraud Prevention Flow

```
Ticket Scan
      ↓
Authenticity Verification
      ↓
Ownership Validation
      ↓
Duplicate Claim Check
      ↓
AI Fraud Risk Analysis
      ↓
[Low Risk]    → Auto Reward Transfer
[Medium Risk] → Assisted Verification
[High Risk]   → Manual Investigation
```

---

## Ticket Lifecycle

```
Unregistered
      ↓
Scanned
      ↓
Verified Ownership
      ↓
Reward Eligible
      ↓
Reward Claimed
      ↓
Archived
```

### Duplicate Attempt Flows
```
Duplicate Ownership Attempt → Ownership Blocked → Fraud Monitoring Triggered
Duplicate Payout Attempt    → Duplicate Payout Blocked → Attempt Logged
```

---

*Document generated from LottoLink hackathon analysis session*
*GLO Automation System Challenge — Paotang Platform*
