<script>
  import Queries from './routes/Queries.svelte';
  import Reports from './routes/Reports.svelte';
  import AdminPanel from "./routes/AdminPanel.svelte";

  let currentPage = 'queries';

  function goTo(page) {
    currentPage = page;
  }
</script>

<nav class="bg-gradient-to-r from-blue-600 to-indigo-700 shadow-lg">
  <div class="max-w-7xl mx-auto px-6 py-3 flex items-center justify-between">
    <h1 class="text-white text-xl font-bold tracking-wide">ITJobs Dashboard</h1>
    <div class="flex gap-4">
      <button
              on:click={() => goTo('queries')}
              class="px-4 py-2 rounded-md font-medium transition text-white hover:bg-blue-500"
              class:bg-blue-500={currentPage === 'queries'}
      >
        Запросы
      </button>

      <button
              on:click={() => goTo('reports')}
              class="px-4 py-2 rounded-md font-medium transition text-white hover:bg-blue-500"
              class:bg-blue-500={currentPage === 'reports'}
      >
        Отчёты
      </button>
      <button
              on:click={() => goTo('admin')}
              class="px-4 py-2 rounded-md font-medium transition text-white hover:bg-blue-500"
              class:bg-blue-500={currentPage === 'admin'}
      >
        Админка
      </button>

    </div>
  </div>
</nav>

<main class="max-w-7xl mx-auto p-6">
  {#if currentPage === 'queries'}
    <Queries />
  {:else if currentPage === 'reports'}
    <Reports />
  {:else if currentPage === 'admin'}
    <AdminPanel />
  {/if}

</main>
