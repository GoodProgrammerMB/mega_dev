# MEGA.dev work (S01E03 "what it shows")

Tickets: three Issues, one per problem found inside support ticket #4471. Counts: one id one report (the export lists 15 reports twice), cause from the tenant's locale and booking form (`causes.ts`), the five one-day reports from `desk-notes.json` in a ticket of their own.

| Issue | Problem | Reports |
|---|---|---|
| [#7](../../issues/7) | Shared parser reads day-first dates month first | 374 |
| [#8](../../issues/8) | booking-v3 form hint says `MM/DD/YYYY` to day-first tenants | 33 |
| [#9](../../issues/9) | Date shifted by exactly one day (not a swap) | 5 |

[`support-kb/`](support-kb/) is the support desk's kb export, unchanged, plus the page below.

Page added to the support kb:

- `support-kb/src/routes/kb/incidents/4471-split/+page.svx` — the page (signed with the course ticket). It uses only the kb's own components, as exported: the desk's `CauseTable` counts the reports at render time.
- `rendered/4471-split.html` — the page as the desk sees it (static copy, no scripts).
