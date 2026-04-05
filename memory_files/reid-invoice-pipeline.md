# Reid Invoice Monitor — Technical Reference

## Overview
Fully automated invoice intake pipeline running on Machine 2 (Reid's Mac Mini).
**Status:** LIVE as of April 2, 2026.
**Script:** `sync/reid_invoice_monitor.py`
**Schedule:** Every 15 minutes via launchd (`com.airg.invoice-monitor`)

## End-to-End Flow
1. **Scan** Reid's inbox via Graph API (delegated refresh token, /me endpoint)
2. **Detect** invoices using keyword matching + known vendor domain list
3. **Download** PDF attachments to `~/airg-invoice-intake/downloads/`
4. **Duplicate check** (3-point): ledger, PW bills by vendor+invoice#+amount, SharePoint filename
5. **Upload** PDF to SharePoint: `3.2 APs (Payables)/PW Vendor Bills to be Entered/02.Vendor bills to be entered in PW/`
6. **Extract** invoice data from PDF via pdfplumber (invoice #, amount, date, vendor)
7. **Match vendor** in PW (full 1,107-vendor cache): PDF text hints → sender domain → subject keywords
8. **Create bill** in Propertyware via REST API (camelCase fields, billSplits required)
9. **Self-verify**: re-read bill from PW, cross-check amount/vendor/ref, check R&M fee markup
10. **Notify Teams**: rich card to 3.2 APs (Payables) channel per invoice + batch summary
11. **Forward** misrouted invoices to accounting@

## Authentication
- **Graph API**: Delegated refresh token flow (Reid Parker Agent app)
  - Client ID: `bed01ec6-df1b-4a0d-9451-db432f0fb8c0`
  - Refresh token: `~/AIRG/email-mcp/.refresh-token` (symlink to teams-mcp)
  - Auto-rotates on each exchange
- **Propertyware API**: Direct REST with headers
  - Client ID: `b9ce316c-7697-4d89-bd63-54cf0e685b43`
  - System ID: `27656196`

## Key PW IDs
- Default Portfolio: `28377099` (ALLINCLUSI)
- Default GL Account: `30607895` (most used with ALLINCLUSI)
- Anago of Northern California: Vendor ID `5097684994`

## R&M Fee Handling
- PW auto-applies markup based on vendor config — Reid does NOT set markup in the API payload
- Verification step checks for `markupPercentage` and `markupDiscountSplit` in bill splits
- If R&M fee is missing, Teams notification shows bold warning for human follow-up
- Bill amount = invoice face value ALWAYS (per CLAUDE.md rules)

## Launchd Config (Machine 2)
- Plist: `~/Library/LaunchAgents/com.airg.invoice-monitor.plist`
- Runs: `/usr/bin/python3 ~/AIRG/sync/reid_invoice_monitor.py --live`
- Interval: 900 seconds (15 min)
- Environment: AZURE_TENANT_ID, AZURE_CLIENT_ID, REFRESH_TOKEN_PATH, PW_CLIENT_ID, PW_CLIENT_SECRET, PW_SYSTEM_ID
- Logs: `~/airg-invoice-intake/logs/`

## Teams Notifications
- Target: 3.2 APs (Payables) channel
  - Team ID: `a5bc95ab-a407-4630-86fe-ca71f38d8eec`
  - Channel ID: `19:56344dc3d861409ca9e08fc0f1717798@thread.skype`
- Per-invoice card: vendor, amount, invoice#, dates, verification status, R&M fee status
- Batch summary: counts of entered/skipped/errors, total amount

## Vendor Matching Strategy (priority order)
1. PDF text hints: hardcoded list (ANAGO, ACE PLUMBING, URBINA, PG&E, SMUD, etc.)
2. Sender email domain: strip suffixes like "NorCal", search PW
3. Subject keywords: skip generic words (invoice, payment, service, etc.)

## Dependencies (Machine 2)
- Python 3.9 + pip packages: requests, pdfplumber, openpyxl
- Installed via `pip3 install --user` (no --break-system-packages on Py 3.9)

## Known Limitations
- PDF attachment to PW bill requires browser automation (API cannot attach files)
- Link-based invoices (utility portals) need browser automation to download
- Vendor matching depends on vendor existing in PW — new vendors need manual setup first
- GL account and portfolio are defaulted — human should verify correct property assignment

## First Successful E2E Test
- Date: April 2, 2026
- Invoice: Anago NorCal Janitorial Service Invoice #95043, $250.00
- PW Bill: #92547 (ID: 7687798786)
- Verification: ALL PASS (exists, amount, vendor, ref)
- Teams notification: Sent to 3.2 APs channel
