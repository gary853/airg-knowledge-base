---
name: Timezone and Calendar Awareness
description: AIRG operates in Sacramento CA Pacific Time — always verify current date/time, never assume. Both Mac Minis are set to America/Los_Angeles.
type: feedback
---

AIRG operates in **Sacramento, California — Pacific Time (America/Los_Angeles)**.

**Why:** Claude made date errors on 2026-03-30 — confused about what day it was, scheduled a Monday intro for Tuesday, gave conflicting information about whether Monday had happened yet. Gary had to correct multiple times.

**How to apply:**
- When the current date matters (scheduling, deadlines, reports), run `date` on the machine to confirm — do not rely on the system-reminder `currentDate` alone
- ALL dates and times referenced to Gary or staff must be in Pacific Time
- Both Mac Minis are set to `America/Los_Angeles` — hardware clocks are correct
- Staff timezones: Kane + Alex = India (IST, PT + 12.5h), Brenda = Philippines (PHT, PT + 15h)
- Never say "tomorrow is Monday" without first confirming what today actually is
- When scheduling launchd jobs, double-check the calendar date against `date` output
