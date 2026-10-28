# MEGA.dev work (S01E03 "what it shows")

Tickets: three Issues, one per problem found inside support ticket #4471 (412 reports; the export lists 15 of them twice, one id is one report).

| Issue | Problem | Reports |
|---|---|---|
| [#1](../../issues/1) | Shared parser reads day-first dates month first | 374 |
| [#2](../../issues/2) | booking-v3 form hint says `MM/DD/YYYY` to day-first tenants | 33 |
| [#4](../../issues/4) | Date shifted by exactly one day (not a swap) | 5 |

Page added to the support kb:

- `src/routes/kb/incidents/4471-split/+page.svx` — the page (signed with the course ticket). It uses only the kb's own components, as exported: the desk's `CauseTable` counts the reports at render time.
- `rendered/4471-split.html` — the page as the desk sees it (static copy, no scripts).
