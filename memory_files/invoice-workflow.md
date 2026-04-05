# Invoice Workflow — AIRG AP/AR

## SharePoint Filing Structure
Site: 3.0 ARIG Trust Financial and Accounting
Path: Shared Documents / 3.2 APs (Payables)

### Vendor Invoices
- **Active (to be entered):** `PW Vendor Bills to be Entered / 02.Vendor bills to be entered in PW/`
  - URL: propertydr.sharepoint.com/sites/3.0Financial/Shared Documents/3.2 APs (Payables)/PW Vendor Bills to be Entered/02.Vendor bills to be entered in PW
  - Has vendor-specific subfolders (e.g., "Independent Plumbing")
- **Completed (after PW entry):** `PW Vendor Bills to be Entered / 01. Completed Vendor Bills Entered/`
  - Monthly subfolders (e.g., "December 2025")

### Utility Bills
- **Active (to be entered):** `PW Utility Bills to be Entered / Utility bills to be entered in PW/`
  - URL: propertydr.sharepoint.com/sites/3.0Financial/Shared Documents/3.2 APs (Payables)/PW Utility Bills to be Entered/Utility bills to be entered in PW
- **Completed (after PW entry):** `PW Utility Bills to be Entered / 3. PW Completed Utility Bills Entered/`
  - Date subfolders

## End-to-End Flow
1. **Detect** invoice email in any distribution list inbox
2. **Download** PDF attachment — or click link to download if no attachment (utilities often send links)
3. **Duplicate check** — CRITICAL: check processed_invoice_ledger.xlsx AND Propertyware for existing bill before proceeding
4. **Upload** to correct SharePoint folder (vendor → vendor folder, utility → utility folder)
5. **Process** via invoice-processing skill (extract data, match vendor/property/GL, dedupe)
6. **Enter** bill into Propertyware via API (attach PDF to the bill)
7. **Move** PDF from active folder → completed folder
8. **Log** to processed_invoice_ledger.xlsx
9. **Auto-forward** misrouted invoices to accounting@ for canonical record

## Link-Based Invoices
Some vendors (especially utilities — PG&E, SMUD) send links instead of PDF attachments.
- Reid (Machine 2) uses browser automation to click the link and download the PDF
- Direct download links: auto-download
- Portal links requiring login: flag for manual download or use stored credentials for known portals

## Responsible Parties
- **Kane Lewis** — Primary. Downloads invoices, files in SharePoint, enters into Propertyware.
- **Reid** — Automated safety net. Catches invoices across all inboxes, downloads, files, processes, enters.
- **Gary** — Oversight. Reviews weekly invoice scorecard.

## Critical Rules
- **DUPLICATE PREVENTION**: Before entering ANY bill, check BOTH the processed_invoice_ledger.xlsx AND Propertyware (pw_list_bills by vendor + invoice number + amount). Vendors frequently send invoices multiple times. NO double entries, NO double payments. If any match → flag as DUPLICATE, do NOT enter.
- **WORK ORDER LINKING**: Always attach the bill to the matching work order. WO can be open OR closed — doesn't matter. Exceeding $250 NTE is fine for bill entry (NTE rule governs work approval, not bill entry).
- **PDF ATTACHMENT**: Every bill entered into Propertyware MUST have the invoice PDF attached. No exceptions. No bill without its PDF.
- **Three-point duplicate check**: (1) processed_invoice_ledger.xlsx, (2) pw_list_bills in Propertyware, (3) SharePoint active folder for same filename.

## Known Problems (as of Apr 2026)
- Invoices arrive in wrong inboxes (info@, maintenance@ instead of accounting@)
- Staff skip downloading/filing invoices
- Link-based invoices from vendors get lost
- Vendors send duplicate invoices — must dedupe before entry
- Kane is bottleneck for manual detection → Reid automates this
