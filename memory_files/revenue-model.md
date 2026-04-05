---
name: AIRG Revenue Model by Persona
description: How AIRG makes money from each persona — fee tiers, GL accounts, passthrough streams, portfolio composition
type: project
---

## Revenue Model Overview

**Total rent roll: ~$63,350/mo** across 86 units, 56 buildings, 25 portfolios.
59% Gary's own properties (River City, Burmaster Family Trust). External clients: 10 Larry (1 unit each), 6 Larry+ (2-4 units), 1 Savvy Sam (5+ units), 0 Big Pete.

**Why:** Understanding revenue by persona drives prioritization — which clients to acquire, retain, cross-sell. Every email, dispute response, and business decision should account for the monetary relationship.

**How to apply:** When responding to tenant/vendor/owner issues, reference the fee structure and contractual position. When crafting campaigns or emails, emphasize value props that align with each persona's revenue contribution.

---

## 9 Revenue Streams

### 1. Management Fees (Primary — recurring)
- **Rate:** 5-8% of collected rent (configured per building in PW)
- **GL:** 4100 (Management Fee Income)
- **Larry Landlord:** Typically 8% (small portfolio, high touch)
- **Savvy Sam:** 6-7% (volume discount, lower touch per unit)
- **Big Pete:** 5-6% (negotiated, large portfolio leverage)
- **Gary's own:** 0% (owner-managed, no fee charged to self)
- **Monthly revenue estimate:** ~$2,500-3,000/mo from external clients

### 2. Leasing Fees (Per-event)
- **Rate:** ~73% of first month's rent (configured per building)
- **GL:** 4200 (Leasing Fee Income)
- **Average per lease:** ~$1,500-1,900
- **Annual estimate:** 15-20 turnovers × $1,700 avg = $25,500-34,000/yr
- **Who pays:** Owner (deducted from first month rent collection)

### 3. Renewal Fees (Per-event)
- **Rate:** $100 flat (some buildings $150)
- **GL:** 4300 (Renewal Fee Income)
- **Annual estimate:** 40-50 renewals × $100 = $4,000-5,000/yr
- **Who pays:** Owner (charged at renewal)

### 4. R&M Markup (Per-invoice)
- **Rate:** Varies by vendor — read from PW vendor `defaultMarkupDiscountPercentage` field
- **GL:** 5810 (R&M Fee Income), GL account ID: 30607895
- **How it works:** When a vendor invoice is processed, AIRG adds a markup % as a separate bill line
- **R&M fee line must NOT include buildingID** (building doesn't belong to ALLINCLUSI portfolio 28377099)
- **Who pays:** Owner (embedded in maintenance costs)

### 5. Late Fees (Passthrough — AIRG keeps 100%)
- **Rate:** Per lease terms (typically $50-75 after 3-5 day grace period)
- **GL:** 4500 (Late Fee Income)
- **Who pays:** Tenant
- **CA law:** Must be "reasonable estimate of costs" — not punitive

### 6. NSF/Returned Check Fees (Passthrough — AIRG keeps 100%)
- **Rate:** $25-50 per occurrence
- **GL:** 4510 (NSF Fee Income)
- **Who pays:** Tenant

### 7. Application Fees (Passthrough — AIRG keeps 100%)
- **Rate:** ~$50 per applicant (CA law caps at actual screening cost)
- **GL:** 4600 (Application Fee Income)
- **Who pays:** Prospective tenant
- **CA law:** AB 1482 — can only charge actual cost of screening

### 8. Convenience/Technology Fees
- **Rate:** Varies (online payment processing fees)
- **Who pays:** Tenant (some passed through to owner)

### 9. Notice/Legal Fees
- **Rate:** Cost recovery for 3-day notices, legal filings
- **GL:** 4010 (Tenant Expense Recovery — standard for ALL charge-backs)
- **Who pays:** Tenant (deducted from deposit or billed)

---

## Revenue by Persona

### Larry Landlord (persona_4) — 64 contacts, 10 active clients
- **Revenue per client:** ~$200-400/mo management fee + leasing/renewal events
- **Margin:** Highest % fee (8%) but lowest absolute dollars
- **Risk:** High churn — price-sensitive, may self-manage
- **Cross-sell:** Insurance review, lending (refi), single property sale (Realtor Randy referral)
- **Lifetime value:** 3-5 years × $3,600/yr = $10,800-18,000

### Savvy Sam (persona_1) — 18 contacts, 1 active client
- **Revenue per client:** ~$800-2,000/mo management fee + higher event fees
- **Margin:** Lower % (6-7%) but much higher absolute dollars
- **Risk:** Expects professional reporting, fast response — will leave for poor service
- **Cross-sell:** 1031 exchange, portfolio expansion, renovation, lending
- **Lifetime value:** 5-10 years × $15,000/yr = $75,000-150,000
- **#1 GROWTH GAP** — only 1 active client, 18 in pipeline

### Big Pete (persona_3) — 3 contacts, 0 active clients
- **Revenue per client:** $3,000-10,000+/mo management fee
- **Margin:** Lowest % (5-6%) but highest absolute revenue per client
- **Risk:** Sophisticated buyer — demands institutional-grade reporting, SLAs
- **Cross-sell:** All divisions (lending, brokerage, 1031, insurance, renovation)
- **Lifetime value:** 10+ years × $50,000+/yr = $500,000+
- **ASPIRATIONAL** — 0 active, need institutional-grade ops first

### Realtor Randy (persona_5) — 11 contacts
- **Revenue:** Referral fees, co-brokerage splits
- **Value:** Lead generation pipeline for owner acquisition
- **Cross-sell:** PM referrals (Larry/Savvy clients), lending referrals

### The Wilson's / Tenants (persona_2) — 303 active
- **Revenue:** Rent (passed to owner) + late fees + app fees + notice fees (kept by AIRG)
- **Value:** Retention = avoided turnover cost ($3,500-5,000 per turn)
- **Lifetime value:** 3.2 years × $2,200/mo = $84,480 rent (owner revenue)
- **AIRG value:** Retention saves $4K/turn × 15-20 turns/yr = $60K-80K avoided cost

### Vendor Vic (persona_8) — 112 active
- **Revenue:** R&M markup on vendor invoices
- **Value:** Reliable vendors = faster WO resolution = better tenant retention = fewer turnovers
- **Risk:** Vendor quality directly impacts owner satisfaction and tenant retention

### Portfolio-Building Patricia (persona_9)
- **Revenue:** Brokerage commissions + PM onboarding (2-4 acquisitions/yr)
- **Value:** Recurring growth — each acquisition = new management contract

### Flip-Focused Frank (persona_6)
- **Revenue:** Hard money lending (8-12% interest), brokerage on purchase/sale
- **Value:** Fast transactions, lending division revenue

### Strategic-Exit Steve (persona_7)
- **Revenue:** Brokerage commissions on portfolio sales, 1031 exchange facilitation
- **Value:** Large transaction fees, potential buyer introductions (Patricia/Savvy/Big Pete)
