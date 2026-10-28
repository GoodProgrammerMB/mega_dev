# MEGA.dev work (S01E03 "what it shows")

Tickets: three Issues, one per problem found inside support ticket #4471. Counts follow the export as the desk made it: 427 rows (412 distinct ids; 15 reports are listed twice and count twice), split by cause from the tenant's locale and booking form (`causes.ts`), with the five one-day reports from `desk-notes.json` taken out of the parser count.

| Issue | Problem | Reports |
|---|---|---|
| [#1](../../issues/1) | Shared parser reads day-first dates month first | 388 |
| [#2](../../issues/2) | booking-v3 form hint says `MM/DD/YYYY` to day-first tenants | 34 |
| [#4](../../issues/4) | Date shifted by exactly one day (not a swap) | 5 |

Page added to the support kb:

- `src/routes/kb/incidents/4471-split/+page.svx` — the page (signed with the course ticket). It uses only the kb's own components, as exported: the desk's `CauseTable` counts the reports at render time.
- `rendered/4471-split.html` — the page as the desk sees it (static copy, no scripts).
