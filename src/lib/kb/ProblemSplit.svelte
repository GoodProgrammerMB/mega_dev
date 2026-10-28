<script lang="ts">
  import reports from '#lib/data/reports-4471.json';
  import notes from '#lib/data/desk-notes.json';
  import { groupByCause, readAs, uniqueById, type Report } from '#lib/kb/causes';
  import { tenantOf } from '#lib/kb/locales';

  // Splits one merged ticket into the problems it really holds. The cause of a report comes from its
  // tenant (causes.ts), never from intake's tag. Reports the desk read as a one-day move
  // (desk-notes.json) are counted where they are filed and footnoted under it, as CauseTable does.
  let { ticket, issues = {} }: { ticket: number; issues?: Record<string, string> } = $props();

  const NAMES: Record<string, string> = {
    parser: 'The shared parser reads day-first dates month first',
    hint: 'The booking-v3 form tells day-first tenants to type month first',
    unplaced: 'No day-first tenant on file',
  };

  const filed = $derived((reports as Report[]).filter((r) => r.mergedInto === ticket));
  const merged = $derived(uniqueById(filed));
  const repeated = $derived(filed.length - merged.length);
  const groups = $derived(groupByCause(merged, tenantOf));
  const mis = $derived(
    groups.reduce((n, g) => n + g.reports.filter((r) => r.meta.intakeTag !== g.cause).length, 0),
  );
</script>

<p class="sum">
  <strong>{merged.length}</strong> reports in #{ticket}
  {#if repeated}(the export lists {repeated} of them twice){/if} ·
  <strong>{groups.length}</strong> problems ·
  intake's tag points to the wrong one for <strong>{mis}</strong>
</p>

<table>
  <thead>
    <tr><th>problem</th><th>reports</th><th>tenants</th><th>seen</th><th>ticket</th></tr>
  </thead>
  <tbody>
    {#each groups as g (g.cause)}
      {@const url = issues[g.cause]}
      <tr>
        <td>{NAMES[g.cause] ?? g.cause}</td>
        <td>{g.reports.length}</td>
        <td>{g.tenants}</td>
        <td>{g.firstSeen} → {g.lastSeen}</td>
        <td>{#if url}<a href={url}>#{url.split('/').pop()}</a>{:else}none yet{/if}</td>
      </tr>
    {/each}
  </tbody>
</table>

{#each groups as g (g.cause)}
  {@const moved = readAs(g, notes.oneDayMove)}
  {#if moved}
    <p class="note">
      {moved} of the {g.reports.length} {g.cause} reports read as a one-day move, not a swap. They are filed
      here, but look like a time-zone problem: check them before you close this ticket.
    </p>
  {/if}
{/each}

<style>
  .sum { font-size: 1.05rem; }
  .note { color: var(--muted); font-size: 0.85em; }
</style>
