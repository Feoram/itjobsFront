<script>
    export let result = [];

    const columns = result.length > 0 ? Object.keys(result[0]) : [];
</script>

<table class="w-full border-collapse border">
    <thead class="bg-gray-200">
    <tr>
        {#each columns as col}
            <th class="border px-2 py-1">{col}</th>
        {/each}
    </tr>
    </thead>
    <tbody>
    {#each result as row}
        <tr>
            {#each columns as col}
                <td class="border px-2 py-1">{row[col]}</td>
            {/each}
        </tr>
    {/each}
    </tbody>
</table>
