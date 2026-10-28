# MEGA.dev work (S01E03 "what it shows")

Tickets: two Issues, one per problem found inside support ticket #4471, counted as the desk's own view (`CauseTable`) counts: one id one report (the export lists 15 reports twice), cause from the tenant's locale and booking form (`causes.ts`). The five reports the desk reads as a one-day move (its footnote) are left out of the parser count and have no ticket of their own; #1 asks for their check.

| Issue | Problem | Reports |
|---|---|---|
| [#1](../../issues/1) | Shared parser reads day-first dates month first | 374 |
| [#2](../../issues/2) | booking-v3 form hint says `MM/DD/YYYY` to day-first tenants | 33 |

Page added to the support kb:

- `src/routes/kb/incidents/4471-split/+page.svx` — the page (signed with the course ticket). It uses only the kb's own components, as exported: the desk's `CauseTable` counts the reports at render time.
- `rendered/4471-split.html` — the page as the desk sees it (static copy, no scripts).
