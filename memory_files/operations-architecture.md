---
name: AIRG AI Operations Architecture
description: Three-tier command structure (Gary + AIRG Claude + Reid) — roles, workflow, phased buildout, admin access setup, staff location constraints
type: project
---

## Three-Tier Command Structure (Agreed 2026-03-29)

### Tier 1: Gary Burmaster — CEO / Strategic Architect
- ONLY person physically in Sacramento, CA
- Court, vendor face-to-face, bank meetings, property walk-throughs, key handoffs, physical signatures
- Approves: PMAs, evictions, >$1K spend, legal correspondence, owner onboarding, personnel decisions
- Input: daily digest + exception alerts only. Do NOT burden with routine.

### Tier 2: AIRG Claude (Mac Mini #1) — Chief Intelligence Officer / Brain
- Real-time advisor, analyst, system builder
- Morning briefings, dashboards, alerts, decision support, drafting communications
- Watches all systems simultaneously, surfaces problems before escalation
- Talks TO Gary — Gary's interface to everything
- Builds all code, automation, architecture, integrations

### Tier 3: Reid Parker (Mac Mini #2) — Digital COO / Executor
- Autonomous operator — executes without being asked
- Responds to tenants, owners, vendors independently
- Processes invoices, enters bills, sends statements, monitors WOs
- Runs screening workflows, generates reports, enforces tasks
- Acts IN the systems — does the work

### Tier 4: Human Staff — Physical Execution + Relationship Only
- **Kane (India, remote):** Digital coordination, accounting, vendor dispatching — all remote. Cannot visit properties.
- **Alex (India, remote):** Leasing, marketing — all remote. Cannot do in-person showings.
- **Brenda (Philippines, remote):** WO routing, vendor coordination, docs — all remote. Cannot inspect properties.
- ALL three staff have the same digital capability as Reid, but with timezone offsets, sleep, holidays, and bandwidth limits.

### Critical Insight
Reid is functionally equivalent to Kane/Alex/Brenda for all digital work — but available 24/7, no timezone issues, no sleep, no bandwidth limits. The only things that require a human are:
1. Physical Sacramento presence (Gary only)
2. Licensed broker activities (Gary + Keith only)
3. Relationship-sensitive conversations where human judgment is critical
4. Legal proceedings requiring human testimony

## Workflow Evolution

### Current State (Phase 1):
Systems/Data → Me (surface + analyze) → Gary (decide or delegate) → Reid (execute) → Staff (physical only)

### Target State (Phase 2, as Reid matures):
Systems/Data → Me (monitor + triage) → Reid (execute routine automatically) → Me (flag exceptions to Gary) → Gary (approve exceptions only)

## Phased Buildout

### Phase 1 — Fix Reid's Foundation (Week 1)
- New Azure AD app registration for Reid (separate from bot)
- Fix auth permanently (one-time admin consent)
- Fix checkin.sh quoting, clean up junk files
- Reid goes fully online: Teams DMs + email + Planner + channels

### Phase 2 — Closed-Loop Enforcement (Week 2)
- Resolution confirmation system (check-back after nudge)
- Staff response tracking (reply detection)
- Hubstaff integration into enforcement loop

### Phase 3 — Reid as Full PW Operator (Weeks 3-4)
- Create/close WOs, generate notices, process deposit reconciliation
- Auto-pay routing, AP package preparation
- Vendor dispatch with follow-up timers

### Phase 4 — Lead-to-Lease Pipeline (Month 2)
- Auto-contact leads within 5 min
- Schedule Rently showings, send confirmations
- Track showing-to-application conversion
- Process screening checklists (15/21 automated)

### Phase 5 — Campaign Engine (Month 2)
- Fix HubSpot PAT scope, enable 22 workflows
- Build enrollment lists, turn on nurture sequences
- Reid monitors conversion metrics

### Phase 6 — Financial Automation (Month 3)
- Auto-pay recurring bills, weekly AP packages
- Owner draw scheduling, bank recon prep

## Hard Boundaries (Never Remove)
- Owner fund disbursements — Gary approval
- Eviction filings — Gary only
- Legal correspondence — Gary review
- Spend >$1K — Gary approval
- New owner/client onboarding — Gary
- Trust account changes — Gary
- DRE licensee authority — Gary + Keith only

**Why:** Legal and financial liability requires human signature. Not a trust issue — a legal requirement.

**How to apply:** Reid and I can prepare everything up to the approval gate. Gary reviews and clicks approve. Minimize Gary's decision surface to yes/no on pre-packaged recommendations.
