<script>
    import { onMount } from 'svelte';

    let section = 'employers';

    // Работодатели
    let employers = [];
    let newEmployer = { name: '', contact_info: '' };

    // Вакансии
    let vacancies = [];
    let newVacancy = {
        title: '', employer_id: '', salary: '', min_salary: '', max_salary: '', location: '', published_date: ''
    };

    // Навыки
    let skills = [];
    let newSkill = { name: '' };

    async function fetchEmployers() {
        const res = await fetch('http://localhost:8080/api/query/SELECT * FROM employers');
        employers = await res.json();
    }

    async function fetchVacancies() {
        const res = await fetch('http://localhost:8080/api/query/SELECT * FROM vacancies');
        vacancies = await res.json();
    }

    async function fetchSkills() {
        const res = await fetch('http://localhost:8080/api/query/SELECT * FROM skills');
        skills = await res.json();
    }

    async function addEmployer() {
        await fetch('http://localhost:8080/api/employers', {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify(newEmployer)
        });
        newEmployer = { name: '', contact_info: '' };
        await fetchEmployers();
    }

    async function addVacancy() {
        await fetch('http://localhost:8080/api/vacancies', {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify(newVacancy)
        });
        newVacancy = { title: '', employer_id: '', salary: '', min_salary: '', max_salary: '', location: '', published_date: '' };
        await fetchVacancies();
    }

    async function addSkill() {
        await fetch('http://localhost:8080/api/skills', {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify(newSkill)
        });
        newSkill = { name: '' };
        await fetchSkills();
    }

    async function deleteEmployer(id) {
        await fetch(`http://localhost:8080/api/employers/${id}`, { method: 'DELETE' });
        await fetchEmployers();
    }

    async function deleteVacancy(id) {
        await fetch(`http://localhost:8080/api/vacancies/${id}`, { method: 'DELETE' });
        await fetchVacancies();
    }

    async function deleteSkill(id) {
        await fetch(`http://localhost:8080/api/skills/${id}`, { method: 'DELETE' });
        await fetchSkills();
    }

    onMount(() => {
        fetchEmployers();
        fetchVacancies();
        fetchSkills();
    });
</script>

<div class="max-w-6xl mx-auto p-6">
    <h1 class="text-3xl font-bold mb-8">Админ-панель управления</h1>

    <div class="flex gap-4 mb-8">
        <button on:click={() => section = 'employers'} class="px-4 py-2 rounded bg-blue-500 text-white hover:bg-blue-600">Работодатели</button>
        <button on:click={() => section = 'vacancies'} class="px-4 py-2 rounded bg-blue-500 text-white hover:bg-blue-600">Вакансии</button>
        <button on:click={() => section = 'skills'} class="px-4 py-2 rounded bg-blue-500 text-white hover:bg-blue-600">Навыки</button>
    </div>

    {#if section === 'employers'}
        <h2 class="text-2xl font-bold mb-4">Работодатели</h2>
        <div class="flex gap-4 mb-6">
            <input type="text" placeholder="Название" bind:value={newEmployer.name} class="border p-2 rounded w-full" />
            <input type="text" placeholder="Контактная информация" bind:value={newEmployer.contact_info} class="border p-2 rounded w-full" />
            <button on:click={addEmployer} class="bg-green-600 text-white px-4 py-2 rounded hover:bg-green-700">Добавить</button>
        </div>
        <table class="w-full bg-white rounded shadow">
            <thead class="bg-gray-100">
            <tr>
                <th class="p-2">ID</th>
                <th class="p-2">Название</th>
                <th class="p-2">Контакты</th>
                <th class="p-2">Действия</th>
            </tr>
            </thead>
            <tbody>
            {#each employers as employer}
                <tr class="hover:bg-gray-50">
                    <td class="p-2">{employer.id}</td>
                    <td class="p-2">{employer.name}</td>
                    <td class="p-2">{employer.contact_info}</td>
                    <td class="p-2">
                        <button on:click={() => deleteEmployer(employer.id)} class="bg-red-500 text-white px-2 py-1 rounded hover:bg-red-600">Удалить</button>
                    </td>
                </tr>
            {/each}
            </tbody>
        </table>
    {/if}

    {#if section === 'vacancies'}
        <h2 class="text-2xl font-bold mb-4">Вакансии</h2>
        <div class="grid grid-cols-2 gap-4 mb-6">
            <input type="text" placeholder="Название вакансии" bind:value={newVacancy.title} class="border p-2 rounded" />
            <input type="number" placeholder="ID работодателя" bind:value={newVacancy.employer_id} class="border p-2 rounded" />
            <input type="number" placeholder="Средняя зарплата" bind:value={newVacancy.salary} class="border p-2 rounded" />
            <input type="number" placeholder="Мин. зарплата" bind:value={newVacancy.min_salary} class="border p-2 rounded" />
            <input type="number" placeholder="Макс. зарплата" bind:value={newVacancy.max_salary} class="border p-2 rounded" />
            <input type="text" placeholder="Локация" bind:value={newVacancy.location} class="border p-2 rounded" />
            <input type="date" placeholder="Дата публикации" bind:value={newVacancy.published_date} class="border p-2 rounded" />
        </div>
        <button on:click={addVacancy} class="bg-green-600 text-white px-6 py-2 rounded hover:bg-green-700 mb-4">Добавить вакансию</button>
        <table class="w-full bg-white rounded shadow">
            <thead class="bg-gray-100">
            <tr>
                <th class="p-2">ID</th>
                <th class="p-2">Название</th>
                <th class="p-2">Локация</th>
                <th class="p-2">Действия</th>
            </tr>
            </thead>
            <tbody>
            {#each vacancies as vacancy}
                <tr class="hover:bg-gray-50">
                    <td class="p-2">{vacancy.id}</td>
                    <td class="p-2">{vacancy.title}</td>
                    <td class="p-2">{vacancy.location}</td>
                    <td class="p-2">
                        <button on:click={() => deleteVacancy(vacancy.id)} class="bg-red-500 text-white px-2 py-1 rounded hover:bg-red-600">Удалить</button>
                    </td>
                </tr>
            {/each}
            </tbody>
        </table>
    {/if}

    {#if section === 'skills'}
        <h2 class="text-2xl font-bold mb-4">Навыки</h2>
        <div class="flex gap-4 mb-6">
            <input type="text" placeholder="Название навыка" bind:value={newSkill.name} class="border p-2 rounded w-full" />
            <button on:click={addSkill} class="bg-green-600 text-white px-4 py-2 rounded hover:bg-green-700">Добавить</button>
        </div>
        <table class="w-full bg-white rounded shadow">
            <thead class="bg-gray-100">
            <tr>
                <th class="p-2">ID</th>
                <th class="p-2">Название навыка</th>
                <th class="p-2">Действия</th>
            </tr>
            </thead>
            <tbody>
            {#each skills as skill}
                <tr class="hover:bg-gray-50">
                    <td class="p-2">{skill.id}</td>
                    <td class="p-2">{skill.name}</td>
                    <td class="p-2">
                        <button on:click={() => deleteSkill(skill.id)} class="bg-red-500 text-white px-2 py-1 rounded hover:bg-red-600">Удалить</button>
                    </td>
                </tr>
            {/each}
            </tbody>
        </table>
    {/if}
</div>

<style>
    button.active {
        @apply bg-blue-700;
    }
</style>
