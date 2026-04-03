# AIRG Financial & Accounting Playbook

**Last Updated:** 2026-04-02 | **Source:** ChatGPT Knowledge Migration (164 conversations, 9,521 lines)

---

## CPA & Accounting Infrastructure

### Kevin Wright (Capstone CPA) — Primary Guidance
- **Email:** kwright@capstone.cpa
- **Role:** Tax planning, depreciation strategy, entity structure, 1031 exchange guidance
- **Primary Functions:** Reconciliation sign-off, amendment coordination, IRS/FTB defense

### ProfitCoach (PM Profit Coach)
- **Role:** Corporate + trust accounting services, reconciliations, reporting, CPA liaison
- **Contact:** corp.allinclusive@pmprofitcoach.com
- **Key Services:** Monthly coaching, financial performance dashboards, QB audit + cleanup

### Misty De La Rosa (SBC — Smart Business Consultants)
- **Email:** misty.delarosa@sbcmn.com | **Phone:** 785-307-1438
- **Role:** Accounting support, trust account closeouts, portfolio reconciliation

---

## Owner Statement Cycle

**Schedule (Fixed):** 10th, 15th, 20th, 25th of each month
- Draft prepared by accounting staff
- Flags: late rent, missing invoices, anomalies
- Distributed to owners
- Security deposits + draws visible on statement

---

## Bill Pay & Invoice Processing

### Timeline
**Friday mornings:** Weekly vendor bill-pay checkpoint
- Invoices must arrive at **accounting@airg** (distribution list)
- Standard payment cycle: **Net 30** from invoice date
- Vendor invoices: due within **48 hours of work completion**

### Invoice Requirements
- **Work orders >$250:** Require written bid + owner approval before work begins
- **Invoices:** Email to accounting@ with completion photos (line-item basis)
- **Invoice content:** Vendor name, invoice #, date, property address, work description, amount
- **Missing invoice #:** Generate fallback ref: `NOINV-MMDDYYYY-Amount` (vendor), `AcctStmt-Last4` (utility), `VendorCode-Date` (recurring)

### Markup Rules
- **R&M Markup:** Standard 15% overhead/service fee on vendor invoices (deducted from payment)
- **Entry rule:** NEVER enter negative amounts. Bill amount = invoice face value. Propertyware calculates markup automatically.
- **Work order linkage:** Attach all bills to matching WO (open or closed). Exceeding $250 NTE is fine for entry.

---

## Trust Account & Security Deposits

### Security Deposit Handling
- Held in trust account; visible on owner statement
- Release: itemized, with deductions explained
- Move-out reconciliation (D&R): must match inspection findings
- **No guaranteed funds:** Direct check payment per agreement (no advance-payment logistics)

### Trust Account Rules (California PM Compliance)
- Maintain segregated trust account with clear GL structure
- Monthly reconciliation required
- All deposits/withdrawals documented
- Compliance with C.A.R. PMA fiduciary standards
- Consumer privacy (CCPA) obligations

---

## Propertyware GL Structure & Configuration

### Account Setup
- **Industry Classification (QBO):** Real Estate – Property Management
- **Revenue Categories:** Management fees, leasing fees, maintenance coordination, markups
- **COGS Structure:** Vendor passthrough vs. corporate expenses (segregated)
- **Owner Draws:** Classified as distributions, not salary/W-2 wages

### Key GL Principles
- Class tracking by property
- Separate trust vs. operating GL chains
- Correct cost basis tracking for depreciation recapture
- Expense coding discipline (no default-to-suggestion practices)

### API Capability
- Propertyware Open API (SOAP-based) available for bill entry
- Vendor bill upload automation possible
- PDF attachment via browser UI (not API)

---

## Depreciation & Tax Strategies

### Depreciation Framework
- **Accumulated depreciation** tracked per property (from HUD settlement documents)
- **Cost segregation analysis:** Available strategy for accelerated deductions
- **Depreciation recapture:** Calculated on sale proceeds; basis = purchase + improvements (not loan balance)
- **1031 exchanges:** Strategic tool for reinvestment without recapture tax
- **Passive activity deductions:** Applicable to real estate rental activity

### Property Sale Documentation
- Closing HUD required for basis validation
- Sale price ≠ loan balance ≠ realized gain
- CPA must reconcile HUD to tax filing (critical audit point)

---

## Accounts Payable & Receivable

### AP Workflow
- Vendor invoice → SharePoint filing (by vendor/utility type)
- Propertyware entry with GL code + property allocation
- Payment on net 30 cycle (Friday morning bill pay)
- PDF attachment mandatory for audit trail

### AR Automation
- Propertyware handles rent collection + ACH tracking
- Late rent flagged on owner statement
- Tenant payment disputes routed to leasing@

### Reconciliation Discipline
- Monthly credit card reconciliation (all cards in QB)
- Bank reconciliation on cash accounts
- Intercompany equity reconciliation (multi-entity structure)
- Trust account rec vs. GL balance (monthly)

---

## Vendor Invoice Rules (Critical Overrides)

**DUPLICATE PREVENTION:** Before ANY entry, check:
1. processed_invoice_ledger.xlsx
2. Propertyware pw_list_bills by vendor + invoice# + amount
3. SharePoint folder for matching filename
→ If match exists: **DO NOT ENTER** (duplicate)

**MISSING AMOUNTS:** Generate fallback reference (never leave blank)

**NEGATIVE AMOUNTS:** Prohibited. Bill entry = invoice face value only.

**PDF ATTACHMENT:** Every PW bill must have invoice PDF attached (non-negotiable)

---

## Entity Structure & Compliance

### Entity Framework
- **All Inclusive Realty Group Inc.** (CA Corp, DRE#01867013/01867031)
- **River City Investments LLC** (investment holding)
- **Burmaster Family Trust** (asset ownership)

### Worker Classification (EDD Defense)
- All team = salary employees or W-9 vendors (clearly documented)
- Vendors: independent contractors with written agreements, invoices, control
- Compensation: owner draws/distributions (not salary/wages)
- CPA + vendor documentation must prove misclassification defense

### Accounting Transition (Nov 2025)
- Moved from prior CPA to **Kevin Wright (Capstone)**
- ProfitCoach conducted QB audit → found 100s of misclassified transactions
- 2024-2025 bookkeeping migration in progress
- CPA demand letter territory: prior firm fabricated sale figures, miscombined properties

---

## Financial Health Metrics (Monitoring)

### Monthly Dashboard Checks
- Maintenance expense trending (benchmark: target % of rent)
- Late rent % (flag if >5%)
- Vendor coordination markup $$
- Trust account reconciliation status
- AP aging (days payable outstanding)
- Owner draw sustainability

### Annual Benchmarks (ProfitCoach)
- Gross margin % (per revenue stream)
- Operating expense ratio
- Cash conversion cycle
- Tax planning effectiveness
- Depreciation recapture exposure

---

## Key Constraints & Guardrails

1. **No Guaranteed Funds:** Minimize upfront bank trips; use standard payment.
2. **$250 WO Threshold:** Gary retains unlimited sign-off authority above threshold.
3. **Fiduciary Compliance:** C.A.R. PMA updated 6/24; old contracts expose to DRE/litigation risk.
4. **QB Essentials Limitation:** Scaling beyond 50 doors may require Plus/Advanced for class/location depth.
5. **EDD Risk:** Trust account funds are corporate assets. Payroll/worker classification documentation is audit-critical.
6. **Depreciation Accuracy:** HUD closing statements = ground truth for basis. CPA must reconcile to tax return.

---

## Quick-Reference Contacts

| Role | Email | Phone | Notes |
|------|-------|-------|-------|
| Kevin Wright (CPA) | kwright@capstone.cpa | — | Tax planning, entity structure |
| ProfitCoach | corp.allinclusive@pmprofitcoach.com | — | QB audit, accounting services |
| Misty (SBC) | misty.delarosa@sbcmn.com | 785-307-1438 | Trust closeouts, portfolio rec |
| Accounting Distribution | accounting@airg | — | Invoice intake, bill pay |
| Maintenance Distribution | maintenance@airg | — | WO routing, vendor dispatch |

---

## Action Items for Implementation

1. **QB Profile:** Correct industry classification to "Real Estate – Property Management" (Essentials → Plus consideration for 1,000-unit scale)
2. **Trust Reconciliation:** Monthly GL reconciliation with bank statements (ProfitCoach oversight)
3. **CPA Transition File:** Build from HUDs + processed_invoice_ledger for Kevin Wright handoff
4. **Vendor Documentation:** Ensure all invoices have PDF attachments in Propertyware (no exceptions)
5. **1031 Strategy:** Coordinate with Kevin Wright for reinvestment timing and entity structure optimization

