# MEGA.dev work (S01E03 "what it shows")

Tickets: two Issues, one per problem found inside support ticket #4471 (412 reports, counted as the desk counts them).

| Issue | Problem | Reports |
|---|---|---|
| [#1](../../issues/1) | Shared parser reads day-first dates month first (5 of these read as a one-day move and need a time-zone check) | 379 |
| [#2](../../issues/2) | booking-v3 form hint says `MM/DD/YYYY` to day-first tenants | 33 |

#3 was folded into #1 and closed.

Page added to the support kb:

- `src/routes/kb/incidents/4471-split/+page.svx` — the page (signed with the course ticket).
- `src/lib/kb/ProblemSplit.svelte` — the component that computes the counts from `reports-4471.json`.
- `rendered/4471-split.html` — the page as the desk sees it (static copy, no scripts).
