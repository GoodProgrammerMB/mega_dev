<script lang="ts">
  import reports from '#lib/data/reports-4471.json';
  import notes from '#lib/data/desk-notes.json';
  import { groupByCause, readAs, uniqueById, type Report } from '#lib/kb/causes';
  import { tenantOf } from '#lib/kb/locales';

  // Splits one merged ticket into the problems it really holds, counted the way the desk's own view
  // (CauseTable) counts: one id, one report; the cause from the tenant (causes.ts), never from
  // intake's tag; reports the desk read as a one-day move (desk-notes.json) stay filed under their
  // tenant's cause, where the desk footnotes them, and get a row of their own here, so the third
  // problem has a count and a ticket. Their row says where the desk filed them.
  let { ticket, issues = {} }: { ticket: number; issues?: Record<string, string> } = $props();

  type Problem = {
    key: string;
    name: string;
    reports: Report[];
    tenants: number;
    firstSeen: string;
    lastSeen: string;
    misTagged: number;
    note: string;
  };

  const filed = $derived((reports as Report[]).filter((r) => r.mergedInto === ticket));
  const merged = $derived(uniqueById(filed));
  const repeated = $derived(filed.length - merged.length);

  const NAMES: Record<string, string> = {
    parser: 'The shared parser reads day-first dates month first',
    hint: 'The booking-v3 form tells day-first tenants to type month first',
    oneDayMove: 'A date moves by exactly one day (not a swap)',
  };

  function summarise(key: string, rs: Report[], tagOf: (r: Report) => string, note: string): Problem {
    const days = rs.map((r) => r.receivedAt.slice(0, 10)).sort();
    return {
      key,
      name: NAMES[key] ?? key,
      reports: rs,
      tenants: new Set(rs.map((r) => r.tenant)).size,
      firstSeen: days[0],
      lastSeen: days[days.length - 1],
      misTagged: rs.filter((r) => r.meta.intakeTag !== tagOf(r)).length,
      note,
    };
  }

  const problems = $derived.by(() => {
    const groups = groupByCause(merged, tenantOf);
    const byCause = groups.map((g) => {
      const moved = readAs(g, notes.oneDayMove);
      return summarise(g.cause, g.reports, () => g.cause, moved ? `${moved} of these are the one-day move, filed here by tenant` : '');
    });
    const moved = merged.filter((r) => notes.oneDayMove.includes(r.id));
    const filedUnder = [...new Set(groups.filter((g) => readAs(g, notes.oneDayMove)).map((g) => g.cause))].join(', ');
    return [
      ...byCause,
      ...(moved.length ? [summarise('oneDayMove', moved, () => 'oneDayMove', `also inside the ${filedUnder} count above`)] : []),
    ];
  });
  const mis = $derived(problems.reduce((n, p) => n + p.misTagged, 0));
</script>

<p class="sum">
  <strong>{merged.length}</strong> reports in #{ticket}
  {#if repeated}(the export lists {repeated} of them twice; one id is one report){/if} ·
  <strong>{problems.length}</strong> problems ·
  intake's tag points to the wrong one for <strong>{mis}</strong>
</p>

<table>
  <thead>
    <tr><th>problem</th><th>reports</th><th>tenants</th><th>seen</th><th>ticket</th><th></th></tr>
  </thead>
  <tbody>
    {#each problems as p (p.key)}
      <tr>
        <td>{p.name}</td>
        <td>{p.reports.length}</td>
        <td>{p.tenants}</td>
        <td>{p.firstSeen} → {p.lastSeen}</td>
        <td>{#if issues[p.key]}<a href={issues[p.key]}>{issues[p.key].split('/').pop() ? `#${issues[p.key].split('/').pop()}` : 'ticket'}</a>{:else}none yet{/if}</td>
        <td class="note">{p.note}</td>
      </tr>
    {/each}
  </tbody>
</table>

<style>
  .sum { font-size: 1.05rem; }
  .note { color: var(--muted); font-size: 0.85em; }
</style>
