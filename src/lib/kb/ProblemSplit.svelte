<script lang="ts">
  import reports from '#lib/data/reports-4471.json';
  import notes from '#lib/data/desk-notes.json';
  import { groupByCause, uniqueById, type Report } from '#lib/kb/causes';
  import { tenantOf } from '#lib/kb/locales';

  // Splits one merged ticket into the problems it really holds. The cause of a report comes from its
  // tenant (causes.ts), never from intake's tag. Reports the desk read as a one-day move
  // (desk-notes.json) are a third problem: a swap cannot move a date by exactly one day.
  let { ticket, issues = {} }: { ticket: number; issues?: Record<string, string> } = $props();

  type Problem = {
    key: string;
    name: string;
    reports: Report[];
    tenants: number;
    firstSeen: string;
    lastSeen: string;
    misTagged: number;
  };

  const filed = $derived((reports as Report[]).filter((r) => r.mergedInto === ticket));
  const merged = $derived(uniqueById(filed));
  const repeated = $derived(filed.length - merged.length);

  const NAMES: Record<string, string> = {
    parser: 'The shared parser reads day-first dates month first',
    hint: 'The booking-v3 form tells day-first tenants to type month first',
    oneDayMove: 'A date moves by exactly one day (not a swap)',
  };

  function summarise(key: string, rs: Report[], tagOf: (r: Report) => string): Problem {
    const days = rs.map((r) => r.receivedAt.slice(0, 10)).sort();
    return {
      key,
      name: NAMES[key] ?? key,
      reports: rs,
      tenants: new Set(rs.map((r) => r.tenant)).size,
      firstSeen: days[0],
      lastSeen: days[days.length - 1],
      misTagged: rs.filter((r) => r.meta.intakeTag !== tagOf(r)).length,
    };
  }

  const problems = $derived.by(() => {
    const moved = merged.filter((r) => notes.oneDayMove.includes(r.id));
    const rest = merged.filter((r) => !notes.oneDayMove.includes(r.id));
    const byCause = groupByCause(rest, tenantOf).map((g) => summarise(g.cause, g.reports, () => g.cause));
    return [...byCause, ...(moved.length ? [summarise('oneDayMove', moved, () => 'oneDayMove')] : [])];
  });
  const mis = $derived(problems.reduce((n, p) => n + p.misTagged, 0));
</script>

<p class="sum">
  <strong>{merged.length}</strong> reports in #{ticket}
  {#if repeated}(the export lists {repeated} of them twice){/if} ·
  <strong>{problems.length}</strong> problems ·
  intake's tag points to the wrong one for <strong>{mis}</strong>
</p>

<table>
  <thead>
    <tr><th>problem</th><th>reports</th><th>tenants</th><th>seen</th><th>ticket</th></tr>
  </thead>
  <tbody>
    {#each problems as p (p.key)}
      <tr>
        <td>{p.name}</td>
        <td>{p.reports.length}</td>
        <td>{p.tenants}</td>
        <td>{p.firstSeen} → {p.lastSeen}</td>
        <td>{#if issues[p.key]}<a href={issues[p.key]}>{issues[p.key].split('/').pop() ? `#${issues[p.key].split('/').pop()}` : 'ticket'}</a>{:else}none yet{/if}</td>
      </tr>
    {/each}
  </tbody>
</table>

<style>
  .sum { font-size: 1.05rem; }
</style>
