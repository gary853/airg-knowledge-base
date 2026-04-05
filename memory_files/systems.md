# AIRG Technology Stack

## Core Systems
| System | Used For | Access |
|--------|----------|--------|
| MS Teams | Primary ops hub, dept channels (1.0-7.0) | Graph API on all machines |
| Outlook | Email, triage folders mirror Teams channels | Graph API + IMAP |
| Propertyware | Trust accounting, WOs, rent collection, owner portal | Custom MCP (API + browser) |
| Rently | Self-showings, vendor access, lockboxes | Custom MCP |
| HubSpot | CRM — investor personas, lead scoring, pipeline | MCP + API |
| DocuSign | Lease agreements, signature workflows | Not connected to AI |
| Hubstaff | Time tracking for team | Partial integration |
| Wise | International payments (some payroll) | Not connected to AI |
| Perfect Wiki | SOPs, playbooks (in Teams) | Via SharePoint |
| Planner | Task tracking, risk dashboard | Graph API scripts |
| QuickBooks Online | Accounting — P&L, BS, GL, AP/AR, bills, invoices, journal entries | Custom MCP (port 3006) — all 3 machines, 3 company files (AIRG/BFT/RCI). See memory/context/quickbooks.md |

## AI Infrastructure
See memory/context/architecture.md for full three-machine setup.

## Teams Channel Structure
- 1.0 Sales and Marketing (1.1-1.4)
- 2.0 Operations Property Management (2.1-2.5)
- 2.5 Operations Real Estate Sales (2.0-2.8)
- 3.0 Trust Financial and Accounting (3.1-3.6)
- 4.0 Team & HR (4.1-4.6)
- 5.0 Leadership (5.1-5.5)
- 6.0 Business Financials and Accounting (6.1-6.7)
- 7.0 Personal (7.1-7.9)
- 8.0 SV Close out
