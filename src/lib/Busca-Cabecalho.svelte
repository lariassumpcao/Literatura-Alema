<script lang="ts">
    import { Input, Label, Toggle, Checkbox, Button  } from 'flowbite-svelte';
    import { createEventDispatcher } from 'svelte';

    const dispatch = createEventDispatcher();

    export let db_date_min: number = 0;
    export let db_date_max: number = 0;


    let author = '';
    let title = '';
    let translations = true;
    let originals = true;
    let date_min = '';
    let date_max = '';
    
    export let show_all = false;

    const do_search = () => {
        if ( 
            (author.length >= 3) || 
            (title.length >= 3) ||

            (date_min.length == 4) || (date_max.length == 4)
            ) {
            dispatch('search-info', {
                author:         author,
                title:          title,
                translations:   translations,
                originals:      originals,
                date_min:       parseInt(date_min),
                date_max:       parseInt(date_max),
            });
        } else {
            dispatch('clear');
        }
    }

    const clear_search = () => {
        dispatch('clear');
        author = '';
        title = '';
        translations = true;
        originals = true;
        date_min = '';
        date_max = '';
    }

    const show_all_fn = (e: Object) => {
        dispatch('show-all', e.target.checked);
    }

    const on_keyup = (e) => {
        do_search();
    }
    
    const on_keyup_date = (e) => {
        if (e.target.value.length == 4) {
            do_search();
        }
    }

    const checkbox_not_selected = 'w-full text-center select-none font-medium focus:ring-4 focus:outline-none inline-flex items-center justify-center px-5 py-2.5 text-sm text-gray-900 bg-white border border-gray-200 dark:border-gray-600 hover:bg-gray-100 focus:ring-gray-200 dark:bg-gray-800 dark:text-gray-400 hover:text-blue-700 focus:text-blue-700 dark:focus:text-white dark:hover:text-white dark:hover:bg-gray-700 dark:focus:ring-gray-700 dark:bg-transparent dark:border-gray-800 dark:hover:border-gray-700 rounded-lg';
    const checkbox_selected     = 'w-full text-center select-none font-medium focus:ring-4 focus:outline-none inline-flex items-center justify-center px-5 py-2.5 text-sm text-white bg-gray-800 border hover:bg-gray-900 focus:ring-gray-300 dark:bg-gray-800 dark:hover:bg-gray-700 dark:focus:ring-gray-700 rounded-lg';

</script>

<div class="mb-6">
    <div class="flex justify-between my-4">
        <div class="w-80">
            <Label for="title">Título</Label>
            <Input on:keyup={on_keyup} bind:value={title} type="text" id='title' placeholder="Título ou parte do título" />
            <div class="flex justify-between gap-3 my-2">
                <Checkbox bind:checked={translations} class='w-1/2' custom>
                    <div class="w-full cursor-pointer">
                        <div class={translations ? checkbox_selected : checkbox_not_selected}>
                            Traduções
                        </div>
                    </div>
                </Checkbox>
                <Checkbox bind:checked={originals} class='w-1/2' custom>
                    <div class="w-full cursor-pointer">
                        <div class={originals ? checkbox_selected : checkbox_not_selected}>
                            Originais
                        </div>
                    </div>
                </Checkbox>
            </div>
        </div>
        <div class="w-80">
            <Label for="author">Autor(a)</Label>
            <Input on:keyup={on_keyup} bind:value={author} type="text" placeholder="Nome ou parte do nome" />
        </div>
        <div class="w-80">
            <Label for="date">Data</Label>
            <div class="flex gap-3">
                <Input on:keyup={on_keyup_date} bind:value={date_min} type="text" placeholder="De ({db_date_min})" />
                <Input on:keyup={on_keyup_date} bind:value={date_max} type="text" placeholder="Até ({db_date_max})" />
            </div>
        </div>
    </div>
    <div class="flex justify-between my-4">
        <Toggle class="my-auto" size="small" on:change={show_all_fn} bind:checked={show_all}>Mostrar todos</Toggle>
        <Button color="alternative" on:click={clear_search}>Limpar</Button>
    </div>
</div>