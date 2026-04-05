# Invoice Processing — Rule Overrides & Bug Fixes
# These rules OVERRIDE any conflicting logic in the airg-ap-ar-manager plugin skills.
# Last updated: April 2, 2026

## OVERRIDE 1: NEVER ENTER A NEGATIVE BILL AMOUNT

**Bug found:** The pw_bill_entry.py script (Step 9 RECORD) has markup math that can produce misleading amounts. Additionally, Claude may incorrectly apply R&M markup to the bill entry amount.

**Rule:** The bill amount entered into Propertyware is ALWAYS the face value on the vendor's invoice. NEVER multiply the amount by the markup percentage. Propertyware calculates R&M markup automatically based on vendor settings configured in PW.

**Examples:**
- Invoice says $355.00, vendor has -15% markup → Enter $355.00 in PW (PW handles the markup)
- Invoice says $500.00, vendor has 10% markup → Enter $500.00 in PW (PW handles the markup)
- If you calculate a negative number at any point during bill entry, STOP. Something is wrong.

**Validation:** After entry, the bill amount in PW should match the invoice face value exactly. The R&M Fee line (GL 5810) is a separate automatic entry by PW.

## OVERRIDE 2: MISSING INVOICE NUMBER FALLBACK

**Problem:** Some vendors don't include invoice or reference numbers on their PDFs. The invoice-processing skill flags these as NEEDS_REVIEW but doesn't provide a fallback.

**Rule:** Never leave the Ref No / Invoice # field blank. Generate a reference using this convention:

| Scenario | Format | Example |
|----------|--------|---------|
| No invoice # found on PDF | NOINV-MMDDYYYY-Amount | NOINV-03152026-355.00 |
| Utility statement (has account #) | AcctStmt-Last4ofAcct | AcctStmt-6533 |
| Recurring service (no unique ref) | VendorCode-ServiceDate | TurfPro-03012026 |

**Date used:** The invoice date from the PDF. If no date found, use the date the invoice was received/processed.

**Uniqueness:** The combination of vendor + generated ref + amount should be unique. If not, append a sequence number: NOINV-03152026-355.00-2

## OVERRIDE 3: PDF ATTACHMENT VIA BROWSER (NOT API)

**Problem:** The Propertyware REST API does NOT support file attachment to bills. Previous attempts to attach PDFs via API failed.

**Rule:** After creating a bill via API, Reid MUST use browser automation (Cowork on Machine 2) to:
1. Navigate to the bill in Propertyware UI
2. Click Edit on the bill
3. Use the Attachments section to upload the invoice PDF
4. Save

**URL pattern:** https://app.propertyware.com/pw/moneyout/bill_detail.do?ID={bill_id}

**Workflow sequence:**
1. pw_create_bill() via API → get bill_id/bill_number
2. Open PW in browser → navigate to bill
3. Attach PDF via UI upload
4. Verify attachment appears
5. Move PDF to completed SharePoint folder

Every bill MUST have its invoice PDF attached. No exceptions. No bill is considered "entered" until the PDF is attached.

## OVERRIDE 4: DUPLICATE PREVENTION — THREE-POINT CHECK

**Problem:** Vendors frequently send the same invoice multiple times, to multiple inboxes. Double-entry = double-payment risk.

**Rule:** Before entering ANY bill, check ALL THREE sources:

1. **processed_invoice_ledger.xlsx** — Match by invoice number AND source PDF filename
2. **Propertyware pw_list_bills** — Match by vendor + invoice number + amount
3. **SharePoint active folder** — Check if same filename already exists in the "to be entered" folder

**If ANY source returns a match → flag as DUPLICATE, do NOT enter.**

**For generated invoice numbers (NOINV-...):** Also check by vendor + amount + date combination, since different filenames could contain the same invoice.

## OVERRIDE 5: WORK ORDER LINKING — OPEN OR CLOSED

**Clarification:** The bill entry skill says to check for "open" work orders. This is too narrow.

**Rule:** Always link bills to matching work orders regardless of WO status (open OR closed). The work order documents the authorization for the work — the bill is the payment for it. A closed WO just means the work is done, not that it can't receive a bill.

**Query approach:** When searching for matching WOs, search by vendor + property without status filter. Then match by vendor, category, and property/unit.
