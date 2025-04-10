<script>
    let selected = "1";
    let result = [];
    let loading = false;
    let error = "";

    async function fetchQuery() {
        loading = true;
        error = "";
        result = [];

        try {
            const res = await fetch(`http://localhost:8080/api/query/${selected}`);
            if (!res.ok) throw new Error("Ошибка при выполнении запроса");
            result = await res.json();
        } catch (e) {
            error = e.message;
        } finally {
            loading = false;
        }
    }

    const queryIds = Array.from({ length: 20 }, (_, i) => i + 1);
</script>

<div class="max-w-6xl mx-auto p-6">
    <h1 class="text-3xl font-extrabold text-gray-800 mb-6 tracking-tight">
        Запросы к IT-вакансиям
    </h1>

    <div class="bg-white shadow-xl rounded-2xl p-6 mb-8 flex flex-col md:flex-row gap-4 items-center">
        <label class="text-lg font-medium text-gray-700">Выберите запрос:</label>
        <select bind:value={selected} class="flex-1 border border-gray-300 rounded-xl px-4 py-2 text-gray-700 shadow focus:outline-none focus:ring focus:ring-blue-300">
            {#each queryIds as id}
                <option value={id}>Запрос {id}</option>
            {/each}
        </select>
        <button
                on:click={fetchQuery}
                class="bg-gradient-to-r from-blue-500 to-blue-700 text-white font-semibold px-6 py-2 rounded-xl shadow hover:from-blue-600 hover:to-blue-800 transition duration-200"
        >
            Выполнить
        </button>
    </div>

    {#if loading}
        <div class="text-center py-10">
            <span class="text-gray-500 text-lg">Загрузка...</span>
        </div>
    {:else if error}
        <div class="bg-red-100 text-red-700 p-4 rounded-xl shadow mb-6">
            {error}
        </div>
    {:else if result.length}
        <div class="overflow-x-auto">
            <table class="min-w-full bg-white rounded-xl shadow-xl overflow-hidden">
                <thead class="bg-blue-100 text-gray-800 text-sm uppercase">
                <tr>
                    {#each Object.keys(result[0]) as col}
                        <th class="px-6 py-3 text-left font-semibold tracking-wide">{col}</th>
                    {/each}
                </tr>
                </thead>
                <tbody class="text-gray-700 divide-y divide-gray-200">
                {#each result as row}
                    <tr class="hover:bg-blue-50">
                        {#each Object.entries(row) as [_, value]}
                            <td class="px-6 py-4 whitespace-nowrap">{value}</td>
                        {/each}
                    </tr>
                {/each}
                </tbody>
            </table>
        </div>
    {:else}
        <div class="text-center text-gray-400 py-10">
            Нет данных. Выберите запрос и нажмите "Выполнить".
        </div>
    {/if}
</div>

<style>
    :global(body) {
        background-color: #f9fafb;
        font-family: 'Inter', sans-serif;
    }
</style>