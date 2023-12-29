<script>
    import { Table, TableBody, TableBodyCell, TableBodyRow, TableHead, TableHeadCell } from 'flowbite-svelte';

    export let results = {};

    let table_body_cell_classes = 'px-4 py-4 whitespace-normal break-words font-medium text-center';
</script>

<div class='block h-full max-w-92'>
    <Table divClass='relative overflow-x-auto h-full' class='table-fixed h-full'>
        <TableHead defaultRow={false} class='sticky top-0'>
            <TableHeadCell padding='px-4 py-3' class='text-center whitespace-pre-line w-3/12'>Título</TableHeadCell>
            <TableHeadCell padding='px-4 py-3' class='text-center whitespace-pre-line w-3/12'>Autor(a)</TableHeadCell>
            <TableHeadCell padding='px-4 py-3' class='text-center whitespace-pre-line w-1/12'>Data da Publicação</TableHeadCell>
            <TableHeadCell padding='px-4 py-3' class='text-center whitespace-pre-line w-1/12'>Língua</TableHeadCell>
            <TableHeadCell padding='px-4 py-3' class='text-center whitespace-pre-line w-2/12'>Local de Edição</TableHeadCell>
            <TableHeadCell padding='px-4 py-3' class='text-center whitespace-pre-line w-2/12'>Observações</TableHeadCell>
        </TableHead>
        <TableBody>
            {#if !(Object.keys(results).length === 0)}
                {#each results.items as result}
                <TableBodyRow>
                    <TableBodyCell tdClass={table_body_cell_classes}>{result.title}</TableBodyCell>
                    <TableBodyCell tdClass={table_body_cell_classes}>{result.author_registry}</TableBodyCell>
                    <TableBodyCell tdClass={table_body_cell_classes}>{result.date == '0' ? '-' : result.date}</TableBodyCell>
                    <TableBodyCell tdClass={table_body_cell_classes}>{result.language}</TableBodyCell>
                    <TableBodyCell tdClass={table_body_cell_classes}>{result.edition_place_city}</TableBodyCell>
                    <TableBodyCell tdClass={table_body_cell_classes}>{result.observation}{#if result.type === 'Tradução'}<br>Tradutor(a): {result.author}{/if}</TableBodyCell>
                </TableBodyRow>
                {/each}
            {:else}
                <TableBodyRow>
                    <TableBodyCell colspan=6 class='text-center align-middle h-full text-lg'>...</TableBodyCell>
                </TableBodyRow>
            {/if}
        </TableBody>
    </Table>
</div>