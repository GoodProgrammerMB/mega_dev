# MEGA.dev work (S01E03 "what it shows")

Tickets: three Issues, one per problem found inside support ticket #4471 (412 reports).

| Issue | Problem | Reports |
|---|---|---|
| [#1](../../issues/1) | Shared parser reads day-first dates month first | 374 |
| [#2](../../issues/2) | booking-v3 form hint says `MM/DD/YYYY` to day-first tenants | 33 |
| [#3](../../issues/3) | Date shifted by exactly one day (not a swap) | 5 |

Page added to the support kb:

- `src/routes/kb/incidents/4471-split/+page.svx` — the page (signature `MarcinB`).
- `src/lib/kb/ProblemSplit.svelte` — the component that computes the counts from `reports-4471.json`.
- `rendered/4471-split.html` — the page as the desk sees it (static copy, no scripts).
