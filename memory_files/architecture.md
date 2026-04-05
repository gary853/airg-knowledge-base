---
name: Reid Parker — Full Technical Architecture
description: Complete technical spec for Reid's Mac Mini #2 setup — scripts, configs, auth, services, file paths, prompts, enforcement system, queue protocol
type: reference
---

## Machine
- **Hostname:** AIRGs-Mac-mini.local (airgmacmini2)
- **IP:** 192.168.1.36 (LAN)
- **SSH:** `ssh airgmacmini2@192.168.1.36` (key-based, no password)
- **Hardware:** Apple M4, 10 cores, 228GB disk (7% used), macOS 26.2
- **User:** airgmacmini2

## Azure AD Auth (rebuilt 2026-03-29)

### Reid Parker Agent App
- **App ID:** `bed01ec6-df1b-4a0d-9451-db432f0fb8c0`
- **Type:** Public client (no client secret, device code flow)
- **Tenant:** `7850e4f9-79e9-4e01-8b10-f20e9fe864be`
- **Admin consent:** Granted org-wide (AllPrincipals)
- **Delegated scopes:** Chat.Read, Chat.ReadWrite, ChannelMessage.Send, Mail.Read, Mail.ReadWrite, Mail.Send, User.Read, User.ReadBasic.All, Tasks.ReadWrite, Files.ReadWrite.All, IMAP.AccessAsUser.All, SMTP.Send
- **Refresh token:** `/Users/airgmacmini2/AIRG/teams-mcp/.reid-refresh-token`
- **Token rotation:** Auto-rotates on each use (DM daemon uses it every 60s). 90-day rolling expiry — will never expire as long as services run.

### Bot App (for client_credentials reads)
- **App ID:** `9af7210f-8eec-44d3-b7f8-a3c9fd409767`
- **Client Secret:** `[REDACTED]`
- **Used by:** DM daemon `getAppToken()` only — reads chats via `/users/{id}/chats`
- **NOT used for:** sends, email, or any delegated actions

### Dual-Auth Pattern in DM Daemon
- `APP_CLIENT_ID` + `APP_CLIENT_SECRET` → client_credentials → app token → READ chats/messages
- `REID_CLIENT_ID` (no secret) → refresh_token → delegated token → SEND messages as Reid
- **oneOnOne filter**: Daemon ONLY processes 1:1 DMs — skips group chats and channel threads (patched 2026-03-30)
- **Echo loop prevention**: 3-layer message filtering (message ID dedup, sent ID tracking, triple sender identity check)
- **Never exposes internal issues**: System prompt prohibits posting auth errors, token issues, or system status to Teams channels

## Reid User Identity
- **UPN:** reid@allinclusiverealtygroup.com
- **User ID:** 9e38681f-e8da-408c-9cc1-2faa995ff69f
- **Display Name:** Reid Parker
- **Role:** AI Operations Coordinator / Digital COO

## Directory Structure

```
~/AIRG/
├── reid-agent/                    # Core agent scripts and config
│   ├── system-prompt.md           # Reid's identity and rules
│   ├── checkin.sh                 # 5-min check-in launcher
│   ├── checkin-prompt.md          # Check-in instructions
│   ├── morning-brief.sh           # 6:30 AM morning brief launcher
│   ├── morning-prompt.md          # Morning brief instructions
│   ├── eod-summary.sh            # 4:30 PM EOD launcher
│   ├── eod-prompt.md             # EOD instructions
│   ├── meeting-review.sh         # Meeting checkpoint launcher
│   ├── mcp-config.json           # MCP server config for claude -p
│   ├── enforcement-log.md        # Task enforcement history
│   ├── last-checkin.md            # Latest check-in summary
│   └── logs/                     # Daily logs (YYYY-MM-DD.log)
├── teams-mcp/                    # Teams MCP server + DM daemon
│   ├── build/index.js            # Teams MCP server (compiled)
│   ├── reid-dm-daemon.cjs        # DM polling daemon (CommonJS)
│   ├── .reid-refresh-token       # Reid's delegated refresh token
│   ├── .refresh-token            # Shared refresh token (also Reid's)
│   ├── .reid-dm-state.json       # DM daemon poll state
│   ├── auth-reid.sh              # Device code auth script (one-time use)
│   └── logs/
│       ├── reid-daemon.log       # Daemon stdout
│       └── reid-daemon-err.log   # Daemon stderr (errors + info)
├── propertyware-mcp/build/       # PW MCP server
├── hubspot-social-mcp/build/     # HubSpot Social MCP server
├── rently-mcp/build/             # Rently MCP server
├── email-mcp-oauth/dist/         # Email MCP server (Graph API)
├── queue/messages.db             # Inter-agent message queue (SQLite)
├── queue-mcp/build/              # Queue MCP server
└── sync/                         # Shared context files
```

## Launchd Services

| Service | Plist | Schedule | Script |
|---------|-------|----------|--------|
| `com.airg.reid-dm-daemon` | `~/Library/LaunchAgents/` | Always on (KeepAlive) | `teams-mcp/reid-dm-daemon.cjs` |
| `com.airg.reid-checkin` | `~/Library/LaunchAgents/` | Every 300s (5 min) | `reid-agent/checkin.sh` |
| `com.airg.reid-morning` | `~/Library/LaunchAgents/` | 6:30 AM daily | `reid-agent/morning-brief.sh` |
| `com.airg.reid-eod` | `~/Library/LaunchAgents/` | 4:30 PM daily | `reid-agent/eod-summary.sh` |
| `com.airg.reid-meetings` | `~/Library/LaunchAgents/` | 9AM/11AM/2PM/4PM | `reid-agent/meeting-review.sh` |

### Service Management Commands
```bash
# Restart a service
ssh airgmacmini2@192.168.1.36 "launchctl bootout gui/\$(id -u) ~/Library/LaunchAgents/com.airg.reid-checkin.plist; sleep 2; launchctl bootstrap gui/\$(id -u) ~/Library/LaunchAgents/com.airg.reid-checkin.plist"

# Check running services
ssh airgmacmini2@192.168.1.36 "launchctl list | grep com.airg"

# View logs
ssh airgmacmini2@192.168.1.36 "tail -50 ~/AIRG/teams-mcp/logs/reid-daemon-err.log"
ssh airgmacmini2@192.168.1.36 "tail -50 ~/AIRG/reid-agent/logs/\$(date +%Y-%m-%d).log"
```

## MCP Config (reid-agent/mcp-config.json)

6 MCP servers registered for `claude -p` invocations:
1. **teams** — `teams-mcp/build/index.js` — Bot app (`9af7210f`) for reads (client_credentials), Reid app (`bed01ec6` via REID_CLIENT_ID) for sends (delegated). Both .refresh-token and .reid-refresh-token point to Reid's token.
2. **email-server** — `email-mcp-oauth/dist/index.js` — Graph API, reid@ identity, Reid app (no secret)
3. **propertyware** — `propertyware-mcp/build/index.js` — PW API creds
4. **hubspot-social** — `hubspot-social-mcp/build/index.js` — HubSpot PAT
5. **queue** — `queue-mcp/build/index.js` — SQLite message queue
6. **rently** — `rently-mcp/build/index.js` — Rently API creds

### Teams MCP Dual-Auth (patched 2026-03-29)
Source: `teams-mcp/src/index.ts` (line 16: REID_CLIENT_ID env var)
- `getAppToken()` → AZURE_CLIENT_ID (bot app) + AZURE_CLIENT_SECRET → client_credentials → reads
- `getDelegatedToken()` → REID_CLIENT_ID (Reid app, no secret) + .refresh-token → sends as Reid
- `getReidDelegatedToken()` → REID_CLIENT_ID + .reid-refresh-token → chat/DM sends as Reid

Claude Desktop (~/.claude/settings.json) has 5 servers:
- Same 4 above + **queue** MCP + **rently** MCP

## Message Queue Protocol

- **DB Path:** `/Users/airgmacmini2/AIRG/queue/messages.db`
- **Engine:** SQLite + WAL journaling, concurrent-safe
- **Schema:** id, timestamp, sender, recipient, subject, body, priority, read_at, ack_at
- **Senders:** REID, AIRG_CLAUDE, GARY
- **Access from Mac Mini #1:** `ssh airgmacmini2@192.168.1.36 "sqlite3 /Users/airgmacmini2/AIRG/queue/messages.db '<SQL>'"`
- **Access from Mac Mini #2:** MCP tools (`queue_send`, `queue_read`) or direct SQLite in daemon

### Send message to Reid from Mac Mini #1:
```bash
ssh airgmacmini2@192.168.1.36 "sqlite3 /Users/airgmacmini2/AIRG/queue/messages.db \"INSERT INTO messages (timestamp, sender, recipient, subject, body, priority) VALUES (datetime('now'), 'AIRG_CLAUDE', 'REID', 'Subject', 'Body text', 'normal');\""
```

### Read Reid's responses:
```bash
ssh airgmacmini2@192.168.1.36 "sqlite3 /Users/airgmacmini2/AIRG/queue/messages.db \"SELECT id, timestamp, sender, subject, substr(body,1,80) FROM messages WHERE sender='REID' ORDER BY id DESC LIMIT 5;\""
```

## Check-in Flow (every 5 minutes)

1. **Queue** — Read pending messages from AIRG_CLAUDE/GARY, process and respond
2. **Enforcement** — Read planner_audit.md + enforcement-log.md, escalate by tier (nudge→alert→escalate→critical), log actions, no duplicate sends
3. **Teams channels** — Check 2.3 Maintenance, 5.0 Leadership, 1.1 Vacancy for actionable items
4. **Email** — Check unread, respond to routine, flag items for Gary
5. **Summary** — Write one-line status to last-checkin.md

## Morning Brief Flow (6:30 AM)

1. Read: daily_briefing.md, planner_audit.md, eod_compliance.md, daily_leasing_metrics.md, enforcement-log.md
2. Check overnight email and Teams
3. Email Gary: subject "Reid Morning Brief — [date]" with top 3 priorities, overnight flags, staff status, vacancy/leasing, deposit/legal
4. DM staff via Teams channels with personalized priorities (Kane→maintenance/financial, Brenda→WO/vendor, Alex→leasing/leads)
5. Post to 5.0 Leadership
6. Queue update to AIRG_CLAUDE

## EOD Summary Flow (4:30 PM)

1. Read: enforcement-log.md, planner_audit.md, eod_compliance.md, last-checkin.md, today's log
2. Score staff: tasks completed vs assigned, Planner updates, response to enforcement
3. Email Gary: subject "Reid EOD Summary — [date]" with completed, not completed, carryover, staff scorecard, tomorrow top 3, risks
4. DM staff with zero Planner updates
5. Post to 5.0 Leadership
6. Queue update to AIRG_CLAUDE

## DM Daemon Upgrade (2026-03-30)
- **Conversation history**: 20 messages per chat, persisted to `.reid-dm-history.json`, loaded on restart
- **Full system prompt**: Uses `system-prompt.md` with ELEVATE, business rules, communication style
- **MCP tools in DMs**: PW (bills, WOs, buildings, vendors, GL), email, queue, Rently, SharePoint, Bash, Read, Write
- **5-minute timeout** (up from 2 minutes)
- **Auto-memory extraction**: Gary's messages scanned for business rules → appended to `learned-from-conversations.md`
- **oneOnOne filter**: Only responds in 1:1 DMs, never group chats or channels
- **Echo loop prevention**: 3-layer (message ID dedup, sent ID tracking, triple sender identity check)
- **API key**: stored at `~/AIRG/reid-agent/.api-key`

## Cowork Task Executor (2026-03-30)
- **Script**: `~/AIRG/reid-agent/cowork-executor.js` (Playwright + Chrome)
- **Daemon**: `com.airg.reid-cowork-executor` (always-on, polls every 30s)
- **Queue**: `~/AIRG/reid-agent/cowork-queue.json` — Reid writes browser tasks here when API fails
- **Results**: `~/AIRG/reid-agent/cowork-results.json` — executor writes results back
- **Session**: `.pw-session.json` — persists PW login cookies across restarts
- **Browser**: Chrome (visible on screen share), 1920x1080 viewport
- **Task types**: `navigate`, `pw_attach_doc`, `screenshot`, `custom` (run arbitrary Playwright script)
- **Auto-fallback**: System prompt instructs Reid to write to cowork-queue when API returns 405/permission error
- **PW login**: Auto-login with stored credentials when navigating to Propertyware

## Full Tool Inventory (deployed 2026-03-30)

### Custom Tools (~/AIRG/reid-agent/)
1. `enforcement-tracker.js` — Task enforcement, fuzzy dedup (60% word overlap), tiered escalation, staff scorecards
2. `cowork-executor.js` — Playwright browser queue daemon (always-on, polls 30s)
3. `bid-comparison.js` — Vendor bid scoring (40% price, 30% timeline, 20% warranty, 10% speed), report generation
4. `lease-tools.js` — Packet validation (21 checks), renewal letters (AB 1482 max increase), move-in packets, proration calc
5. `financial-anomaly-detector.js` — Duplicate bills, unusual charges (>2x vendor avg), missing WO linkage
6. `vendor-onboarding.js` — W-9/COI intake workflow, doc tracking, PW vendor creation
7. `zillow-automation.js` — Zillow Rental Manager Playwright (list, update, deactivate, screenshot)
8. `insurance-tracker.js` — Policy tracking, auto-reminders at 60/30/7 days before expiry
9. `property-tools.js` — Zero-activity detector, lockbox code database, showing follow-up generator (48h + 7d cold)
10. `hubstaff-monitor.js` — Staff hours + activity tracking (PENDING: needs Hubstaff API token — Kane must add Reid as user first)
11. `zinspector-automation.js` — Z-Inspector Playwright (login, list properties, deactivate units, close docs, screenshot). Login: Reid@/Goldsuperior09!. Session persisted at `.zinspector-session.json`

### Shell Scripts (~/AIRG/reid-agent/)
12. `checkin.sh` + `checkin-prompt.md` — 5-min enforcement cycle
13. `morning-brief.sh` + `morning-prompt.md` — 6:30 AM daily
14. `eod-summary.sh` + `eod-prompt.md` — 4:30 PM daily
15. `meeting-review.sh` — 9/11/2/4 meeting checkpoints
16. `monday-intro.sh` + `monday-intro-prompt.md` — one-time team intro (scheduled Mar 31 6:15 AM)

### Python Sync Scripts (~/AIRG/sync/) — copied from Mac Mini #1
17. `deposit_reconciliation.py` (1,609 lines) — CA §1950.5, depreciation, GL entries, HTML statements
18. `zinspector_sync.py` (978 lines) — inspection PDF detection, ticket creation
19. `wo_auto_dispatch.py` — vendor dispatch automation
20. `vendor_followup.py` — vendor follow-up timers
21. `leasing_emails.py` — 17 ELEVATE-branded templates
22. `leasing_approval.py` — lease approval workflow
23. `leasing_tasks.py` — leasing Planner task automation
24. `turnover_service_hub.py` — 11-step turnover lifecycle
25. `maintenance_digest.py` — WO aging + vendor performance
26. `staff_dashboards.py` — per-staff operational dashboards
27. `planner_tasks.py` — Planner task creation/management
28. `email_triage.py` — email classification and routing
29. `vacancy_dashboard.py` — vacancy pipeline (deduped, correct stages)
30. `leasing_report.py` — weekly leasing velocity (deduped)
31. `pw_hubspot_sync.py` — full PW↔HubSpot sync (with tenant association fix)
32. `teams_notify.py` — Teams channel posting helper
33. `social_content_calendar.py` — AI social media content generation

### Reference Files
- `lease-proration-rules.md` — Standard proration, mid-month, Section 8, leap year, deposits, AB 1482
- `memory-ap-invoice-rules.md` — GL mapping, terms, workflow, WO linkage, SharePoint folders
- `.api-key` — Anthropic API key for Claude CLI
- `.zinspector-session.json` — persisted Z-Inspector login session
- `.pw-session.json` — persisted Propertyware login session

### Cowork Extensions (Claude Desktop App)
- Chrome Control — full browser automation
- Desktop Commander — Mac desktop control (mouse, keyboard, windows)
- OSAScript — AppleScript (control any Mac app)
- PDF Filler — read/fill PDF forms
- PowerPoint — create/edit presentations
- iMessage — read/send iMessages

### Cowork Skills (Custom AIRG Plugins)
- AP/AR Manager — PW bill entry with JS workarounds, invoice PDF extraction, `/enter-bill`, `/process-invoices`
- HubSpot Ops — contact sync, pipeline management, campaigns, `/hs-audit`, `/hs-sync`
- Marketing Coordinator — blog, social, SEO, campaigns, `/blog`, `/social`, `/calendar`, `/campaign`
- Propertyware — 22 PW tools via Python MCP server

## Enforcement Tracker (Phase 2 — built 2026-03-29)

### Database
- **Path:** `~/AIRG/reid-agent/enforcement.db` (SQLite + WAL)
- **Tables:** `enforcement_actions` (per-action tracking), `staff_response_tracking` (daily per-staff scorecard)
- **Script:** `~/AIRG/reid-agent/enforcement-tracker.js` (Node.js + better-sqlite3)

### Commands
```bash
node enforcement-tracker.js log <task_id> <title> <assignee> <tier> [channel] [msg_id]   # Log action (skips duplicates)
node enforcement-tracker.js response <task_id> <assignee> <summary>                       # Log staff response
node enforcement-tracker.js resolve <task_id>                                              # Mark resolved
node enforcement-tracker.js overdue                                                        # Get actions past followup deadline
node enforcement-tracker.js mark-escalated <action_id>                                     # Prevent re-processing
node enforcement-tracker.js scorecard                                                      # Today's staff response rates
node enforcement-tracker.js summary                                                        # Full status → enforcement-summary.md
```

### Auto-Escalation Timers
- NUDGE → 4 hours → auto-escalate to ALERT if no response
- ALERT → 24 hours → auto-escalate to ESCALATE if no response
- ESCALATE → 48 hours → auto-escalate to CRITICAL if no response
- CRITICAL → 24 hours → DM Gary again if still no response

### Monday Introduction
- **Script:** `~/AIRG/reid-agent/monday-intro.sh` + `monday-intro-prompt.md`
- Posts to: 5.0 Leadership, 2.0 Operations, 1.0 Sales & Marketing
- Emails Gary confirmation
- Queue update to AIRG_CLAUDE
- **Run manually or schedule as one-time launchd**

## Enforcement Tiers

| Days Overdue | Tier | Action |
|-------------|------|--------|
| 1-2 | NUDGE | Post in assignee dept channel |
| 3-5 | ALERT | Post in dept channel + tag assignee |
| 7-13 | ESCALATE | DM Gary with subject/urgency/context/recommendation/deadline |
| 14+ | CRITICAL | DM Gary daily, flag in all summaries |

Rules: One message per tier transition. Check enforcement-log.md before sending. Never re-send same tier. Log every action with timestamp.

## Hard Boundaries (Tier 3 — escalate to Gary)

- Payments, refunds
- Contracts, PMAs, leases
- Record deletion
- HR actions
- Trust account changes
- DRE licensee authority
- Spend >$1,000
- Owner fund disbursements
- Eviction filings
- Legal correspondence

## API Credentials

| System | Key Detail |
|--------|-----------|
| Anthropic | `sk-ant-api03-1EQ33_0KXV...` (in all .sh scripts) |
| Propertyware | Client: `b9ce316c-...`, Secret: [REDACTED], System: `27656196` |
| HubSpot | PAT: `pat-na1-6381b16a-...` |
| Rently | Email: reid@, Password: `Goldsuperior09!` |
| Azure (Reid app) | Client: `bed01ec6-...`, no secret |
| Azure (Bot app) | Client: `9af7210f-...`, Secret: `[REDACTED]` |

## Troubleshooting

### Auth errors (AADSTS65001 / consent_required)
- Fixed permanently on 2026-03-29. Admin consent is org-wide.
- If this recurs: token expired (90 days unused) → re-run auth-reid.sh

### DM daemon crash-looping
- Check: `tail -20 ~/AIRG/teams-mcp/logs/reid-daemon-err.log`
- Common: .cjs extension required (package.json has "type": "module")
- Restart: `launchctl bootout gui/$(id -u) ~/Library/LaunchAgents/com.airg.reid-dm-daemon.plist && sleep 2 && launchctl bootstrap gui/$(id -u) ~/Library/LaunchAgents/com.airg.reid-dm-daemon.plist`

### Checkin not running
- Check: `launchctl list | grep reid-checkin`
- Check stderr: `cat ~/AIRG/reid-agent/logs/checkin-stderr.log`
- Prompt is in separate .md file now — no more shell escaping issues

### Email MCP failing
- Uses Graph API (not IMAP) via `email-mcp-oauth/dist/index.js`
- Refresh token at `.reid-refresh-token` — same file the daemon rotates
- No client_secret needed (public client)
- If token stale: daemon auto-rotates every 60s, so check if daemon is running first

### Queue not reachable from Mac Mini #1
- SSH must work: `ssh airgmacmini2@192.168.1.36 echo ok`
- DB path: `/Users/airgmacmini2/AIRG/queue/messages.db`
- If locked: WAL mode handles concurrency, but check if a process has exclusive lock
