# QuickBooks Online Integration

## Status: LIVE (Production) — All 3 Company Files Connected
Deployed: 2026-04-04

## Company Files

| Company | Realm ID | QBO Entity Name |
|---------|----------|-----------------|
| All Inclusive Realty Group Inc (AIRG) | `9341453156420064` | All Inclusive Realty Group.Inc |
| Burmaster Family Trust (BFT) | `9341453164927951` | Burmaster Family Trust |
| River City Investments (RCI) | `9341453164940124` | River City Investments |

## Intuit App Registration
- **App Name:** AIRG Operations Hub
- **Environment:** Production
- **Intuit App ID:** Check Intuit Developer Portal → My Apps
- **Production Client ID:** `ABqVIlFDtP3UTuJodlttxlq3HGBCw2yGZgoWCoGmbagyJgt3cT`
- **Production Client Secret:** `[REDACTED]`
- **Sandbox Client ID (DO NOT USE for production):** `ABu6SI6qggCGWNt0U1VduZw6flZxpsGPc4ZQOfaP9hUrHPwkLm`
- **Scope:** `com.intuit.quickbooks.accounting`
- **Redirect URI (Playground):** `https://developer.intuit.com/v2/OAuth2Playground/RedirectUrl`
- **Redirect URI (Local — not working for production):** `http://localhost:8080/callback`

## Token Management
- **Access tokens:** 1-hour TTL, auto-refreshed by MCP server (~5 min before expiry)
- **Refresh tokens:** 101-day rolling TTL (8,726,400 seconds). Each refresh rotates the token and resets the clock.
- **Token file:** `~/AIRG/quickbooks-mcp/tokens.json` (keyed by realm_id)
- **Critical:** If no API call is made for 101 days, refresh tokens expire and manual re-auth via Intuit OAuth Playground is required.

## MCP Server

### Location (all machines)
```
~/AIRG/quickbooks-mcp/
├── src/
│   ├── index.ts          # MCP server — 20 tools, StreamableHTTPServerTransport
│   └── auth.ts           # OAuth flow (authorization code exchange, token refresh)
├── build/                # Compiled JS
├── tokens.json           # Live tokens for all 3 companies
├── package.json
└── tsconfig.json
```

### Runtime
- **Transport:** HTTP (StreamableHTTPServerTransport)
- **Port:** 3006
- **API Base:** `https://quickbooks.api.intuit.com` (production)
- **Node entry:** `build/index.js --http 3006`

### Available MCP Tools (20)
| Tool | Description |
|------|-------------|
| `company_info` | Company details, address, fiscal year |
| `profit_and_loss` | P&L report by date range |
| `balance_sheet` | Balance sheet at a point in time |
| `cash_flow` | Statement of cash flows |
| `general_ledger` | GL detail report |
| `trial_balance` | Trial balance report |
| `ap_aging` | Accounts payable aging summary |
| `ar_aging` | Accounts receivable aging summary |
| `tax_summary` | Tax liability summary |
| `list_accounts` | Chart of accounts |
| `list_vendors` | All vendors |
| `list_customers` | All customers |
| `list_items` | Products and services |
| `list_invoices` | Invoices by date range |
| `list_bills` | Bills by date range |
| `list_payments` | Payments received |
| `list_bill_payments` | Bill payments made |
| `list_journal_entries` | Journal entries |
| `create_journal_entry` | Create a new journal entry |
| `query` | Raw QBO query (SQL-like syntax) |

### LaunchAgent (Gary's Laptop)
- **Plist:** `~/Library/LaunchAgents/com.airg.mcp.quickbooks.plist`
- **KeepAlive:** true (auto-restarts on crash)
- **Logs:** `~/AIRG/quickbooks-mcp/stdout.log`, `stderr.log`

## Deployment Across Machines

| Machine | Path | LaunchAgent | Status |
|---------|------|-------------|--------|
| Machine 3 (Gary's Laptop) | `~/AIRG/quickbooks-mcp/` | `com.airg.mcp.quickbooks.plist` | Running on port 3006 |
| Machine 1 (Mac Mini #1) | `~/AIRG/quickbooks-mcp/` | Needs setup | Tokens deployed, server not auto-started |
| Machine 2 (Reid's Mac Mini) | `~/AIRG/quickbooks-mcp/` | Needs setup | Tokens deployed, server not auto-started |

## Re-Authorization Procedure
If refresh tokens expire (101 days of inactivity), re-auth requires:
1. Go to Intuit OAuth 2.0 Playground: `https://developer.intuit.com/app/developer/playground`
2. Select **AIRG Operations Hub (Production)**
3. Check scope: `com.intuit.quickbooks.accounting`
4. Click "Get Authorization Code" → select company → click Connect
5. The playground redirects back with `?code=...&realmId=...`
6. Exchange code for tokens via curl:
   ```bash
   curl -s -X POST https://oauth.platform.intuit.com/oauth2/v1/tokens/bearer \
     -H "Accept: application/json" \
     -H "Content-Type: application/x-www-form-urlencoded" \
     -H "Authorization: Basic $(echo -n 'CLIENT_ID:CLIENT_SECRET' | base64)" \
     -d "grant_type=authorization_code&code=AUTH_CODE&redirect_uri=https://developer.intuit.com/v2/OAuth2Playground/RedirectUrl"
   ```
7. Save tokens to `tokens.json` keyed by realm_id
8. Repeat for each company file
9. SCP tokens.json to both Mac Minis

**Note:** The `http://localhost:8080/callback` redirect URI does NOT work for production OAuth through AppCenter. Always use the Playground HTTPS redirect for re-auth.

## Known Issues
- Intuit Developer Portal pages have rendering issues (blank white after scroll). Workaround: use `window.scrollTo(0,0)` or interact via element refs, not coordinate scrolling.
- Production App URLs (Host domain, Launch URL, etc.) require HTTPS real domains — can't use localhost. These fields are left empty; not required for API-only integration.
