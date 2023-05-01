<script>
    import Cabecalho from '$lib/Busca-Cabecalho.svelte';
    import Corpo from '$lib/Busca-Corpo.svelte';

    import json_data from '$lib/assets/bibliotecas-filtrado-sample.json';

    /*console.log(json_data);*/

    let db_date_min = 9999;
    let db_date_max = 0;

    let results = {};
    let show_all = false;
    let active_search = false;

    let last_search_criteria = {};

    for (const [idx, item] of Object.entries(json_data)) {
        let date = parseInt(item.date);
        
        if (item.date == 0) {
            continue
        }

        if (date < db_date_min) { db_date_min = date; }
        if (date > db_date_max) { db_date_max = date; }
    }

    const do_search = (e) => {
        // search criteria
        last_search_criteria = e.detail;
        active_search = true;

        process_search(last_search_criteria);
    }

    const process_search = (criteria) => {
        // filter json data
        let filtered_data = json_data.filter(
            (item) => {
                // "true" filters will appear in the table
                let filter_author = criteria.author !== '' ? item.author_registry.toLowerCase().indexOf(criteria.author.toLowerCase()) !== -1 : true;
                let filter_translator = criteria.author !== '' ? item.author.toLowerCase().indexOf(criteria.author.toLowerCase()) !== -1 : true;
                
                let filter_title = criteria.title !== '' ? item.title.toLowerCase().indexOf(criteria.title.toLowerCase()) !== -1 : true;
                
                let filter_translations = (item.type.toLowerCase() == 'tradução') && criteria.translations;
                let filter_originals = (item.type.toLowerCase() == 'publicação') && criteria.originals;

                let filter_date_min = true;
                let filter_date_max = true;
                if (item.date != '0') {
                    filter_date_min = criteria.date_min ? parseInt(item.date) >= criteria.date_min : true;
                    filter_date_max = criteria.date_max ? parseInt(item.date) <= criteria.date_max : true;
                }

                //console.log(item);
                //console.log(filter_author, filter_title, filter_translations, filter_originals, filter_date_min, filter_date_max);

                return (filter_author || filter_translator) && filter_title && (filter_translations || filter_originals) && filter_date_min && filter_date_max
            }
        );

        results = {
            'criteria': criteria,
            'items': filtered_data
        };
    };
    
    const clear_search = (e) => {
        results = {};
        active_search = false;
        show_all = false;

    };

    const show_all_fn = (e) => {
        show_all = e.detail;

        console.log("Teste: " + show_all + ", " + active_search);

        if (show_all) {
            results = {'items':json_data};
        } else {
            if (active_search) {
                process_search(last_search_criteria);
            } else {
                results = {};
            }
        }
    };


</script>

<div class="">
    <h2 class="text-2xl font-bold">Ferramenta de busca</h2>
    <Cabecalho 
        db_date_min='{db_date_min}' db_date_max='{db_date_max}' {show_all}
        on:search-info={do_search} 
        on:clear={clear_search}
        on:show-all={show_all_fn}
    />

    <Corpo {results}/>
</div>