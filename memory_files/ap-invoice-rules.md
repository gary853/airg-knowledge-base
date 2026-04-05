---
name: Reid AP Invoice Entry Rules
description: GL account mapping, default terms, bill workflow, WO linkage rules, SharePoint folders, attachment requirements — trained by Gary 2026-03-30
type: project
---

## GL Account Mapping (Standard Coding Rules)

| Work Type | GL Account |
|-----------|-----------|
| Plumbing | 6780 |
| General Repairs / Handyman | 6460 |
| HVAC / Heating & Air | 6500 |
| Electrical | 6321 |
| Appliance Repair | 6150 |
| Roof Repair | 6890 |
| Landscaping | 6570 |
| Cleaning | search "cleaning" in pw_search_gl_accounts |
| Pest Control | search "pest" in pw_search_gl_accounts |
| Painting | search "paint" in pw_search_gl_accounts |

**Fallback rule:** When in doubt, use `pw_search_gl_accounts` with the work type as search term. Never guess a GL code.

## Default Terms
Net 30 unless the vendor invoice specifies otherwise. Always defer to what's printed on the invoice.

## Bill Approval Workflow
- Enter the bill in Propertyware. Do NOT create bill payments.
- Payment runs happen Friday mornings — Gary and Kane handle payments.
- Reid's job stops at bill entry.
- Post a batch summary to Teams channel 2.1 EA when a batch is completed.

## Work Order Linkage
- Always link a WO if one exists — search `pw_search_work_orders` with building ID and WO number from the invoice.
- Recurring service agreements (landscaping, pest control) — link to WO if one exists, otherwise enter without.
- **CRITICAL:** WO numbers printed on invoices are NOT Propertyware internal IDs. Always use `pw_search_work_orders` to find the real PW internal ID first. Never use the invoice WO number directly in `pw_create_bill`.

## Invoice Attachment
Every bill entered MUST have the invoice PDF attached directly to the bill record in Propertyware. This is non-negotiable.

## SharePoint Invoice Folders
- **Drive ID:** `b!3BvOeT4i9Ey9FCLbzgsajqwpiV-bytVCtqLajD9Vp1pvDddmhTMdT5wE3C8gp5y6`
- **Pending folder:** `3.2 APs (Payables)/PW Vendor Bills to be Entered/02.Vendor bills to be entered in PW`
- **Completed folder:** `3.2 APs (Payables)/PW Vendor Bills to be Entered/01. Completed Vendor Bills Entered`
- After entry, move completed PDFs from pending to completed folder.
- Always run `dry_run=true` first, present results to Gary, then `dry_run=false` after confirmation.

## R&M Fee (from Teams bot memory)
- R&M fees auto-read from PW vendor `defaultMarkupDiscountPercentage` field
- ALLINCLUSI portfolio = 28377099
- R&M Fee GL 5810 = 30607895
- R&M fee line in bill splits must NOT include buildingID (building doesn't belong to ALLINCLUSI portfolio)
