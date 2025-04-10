<script>
    import {onMount, tick} from 'svelte';
    import {Chart, registerables} from 'chart.js';

    Chart.register(...registerables);

    let reportType = 'salary';
    let chart;
    let chartData = [];
    let result = [];
    let loading = false;
    let error = '';

    // Filters
    let minSalary = '';
    let maxSalary = '';
    let location = '';
    let skill = '';
    let minCount = 1;
    let chartEl;

    async function fetchReport() {
        loading = true;
        error = '';
        result = [];
        chartData = [];
        if (chart) chart.destroy();

        let url = '';
        if (reportType === 'salary') {
            url = `http://localhost:8080/api/report/salary-by-location?min=${minSalary}&max=${maxSalary}`;
        } else if (reportType === 'vacancies') {
            url = `http://localhost:8080/api/report/vacancies-with-skills?location=${location}&skill=${skill}`;
        } else if (reportType === 'skills') {
            url = `http://localhost:8080/api/report/skill-demand?min_count=${minCount}`;
        }

        try {
            const res = await fetch(url);
            if (!res.ok) throw new Error('Ошибка запроса');
            const data = await res.json();
            result = data;

            if (reportType === 'salary' || reportType === 'skills') {
                await tick();
                drawChart(data);
            }
        } catch (e) {
            error = e.message;
        } finally {
            loading = false;
        }
    }

    async function drawChart(data) {
        await tick();
        const ctx = chartEl.getContext('2d');

        chart = new Chart(ctx, {
            type: 'bar',
            data: {
                labels: data.map(d => d.location || d.name),
                datasets: [{
                    label: reportType === 'salary' ? 'Средняя зарплата' : 'Востребованность',
                    data: data.map(d => d.avg_salary || d.demand),
                    backgroundColor: 'rgba(59, 130, 246, 0.6)',
                }]
            },
            options: {
                responsive: true,
                plugins: { legend: { display: false } },
                scales: { y: { beginAtZero: true } }
            }
        });
    }
</script>

<div class="max-w-7xl mx-auto px-6 py-8">
    <div class="bg-white shadow-xl rounded-2xl p-8">
        <h1 class="text-4xl font-bold text-gray-800 mb-6">📊 Интерактивные отчёты</h1>

        <div class="flex flex-col lg:flex-row items-center gap-6 mb-6">
            <label class="text-lg font-medium text-gray-700">Тип отчёта:</label>
            <select bind:value={reportType} class="border rounded-lg px-4 py-2 text-gray-800 shadow-sm">
                <option value="salary">💰 Зарплаты по локациям</option>
                <option value="vacancies">📄 Вакансии и навыки</option>
                <option value="skills">📈 Востребованные навыки</option>
            </select>
        </div>

        {#if reportType === 'salary'}
            <div class="grid grid-cols-1 md:grid-cols-2 gap-4 mb-6">
                <input type="number" placeholder="Мин. зарплата" bind:value={minSalary}
                       class="border rounded-lg px-4 py-2 shadow-sm"/>
                <input type="number" placeholder="Макс. зарплата" bind:value={maxSalary}
                       class="border rounded-lg px-4 py-2 shadow-sm"/>
            </div>
        {:else if reportType === 'vacancies'}
            <div class="grid grid-cols-1 md:grid-cols-2 gap-4 mb-6">
                <input type="text" placeholder="Локация" bind:value={location}
                       class="border rounded-lg px-4 py-2 shadow-sm"/>
                <input type="text" placeholder="Навык" bind:value={skill}
                       class="border rounded-lg px-4 py-2 shadow-sm"/>
            </div>
        {:else if reportType === 'skills'}
            <div class="mb-6">
                <input type="number" placeholder="Минимум вхождений" bind:value={minCount}
                       class="border rounded-lg px-4 py-2 shadow-sm"/>
            </div>
        {/if}

        <button
                on:click={fetchReport}
                class="bg-gradient-to-r from-blue-600 to-indigo-600 text-white font-semibold px-6 py-3 rounded-xl shadow-md hover:from-blue-700 hover:to-indigo-700 transition"
        >
            📥 Построить отчёт
        </button>

        {#if loading}
            <p class="mt-6 text-gray-500">Загрузка...</p>
        {:else if error}
            <p class="mt-6 text-red-600">{error}</p>
        {:else if result.length && (reportType === 'salary' || reportType === 'skills')}
            <div class="mt-8 bg-white p-4 rounded-xl shadow-xl">
                <canvas bind:this={chartEl} height="300"></canvas>
            </div>
        {:else if result.length && reportType === 'vacancies'}
            <div class="overflow-x-auto mt-6 rounded-xl shadow-xl">
                <table class="min-w-full bg-white">
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
        {/if}
    </div>
</div>

<style>
    :global(body) {
        background-color: #f9fafb;
        font-family: 'Inter', sans-serif;
    }
</style>
