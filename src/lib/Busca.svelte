<script>
    import Cabecalho from '$lib/Busca-Cabecalho.svelte';
    import Corpo from '$lib/Busca-Corpo.svelte';

    import json_data from '$lib/assets/banco-de-dados-completo.json';

    let db_date_min = 9999;
    let db_date_max = 0;

    let results = {};

    let last_search_criteria = {};

    let get_min_max_dates = (data) => {
        for (const [idx, item] of Object.entries(data)) {
            let date = parseInt(item.date);
            
            if (item.date == 0) {
                continue
            }

            if (date < db_date_min) { db_date_min = date; }
            if (date > db_date_max) { db_date_max = date; }
        }
        return [db_date_min, db_date_max]
    }
    [db_date_min, db_date_max] = get_min_max_dates(json_data);

    const do_search = (e) => {
        // search criteria
        last_search_criteria = e.detail;

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

                let filter_observation = criteria.observation !== '' ? item.observation.toLowerCase().indexOf(criteria.observation.toLowerCase()) !== -1 : true;

                let filter_date_min = true;
                let filter_date_max = true;
                if (item.date != '0') {
                    filter_date_min = criteria.date_min ? parseInt(item.date) >= criteria.date_min : true;
                    filter_date_max = criteria.date_max ? parseInt(item.date) <= criteria.date_max : true;
                }
                
                let filter_origin = criteria.origin.includes(item.origin);

                return (filter_author || filter_translator) && filter_title && (filter_translations || filter_originals) && filter_date_min && filter_date_max && filter_origin && filter_observation
            }
        );
        
        [db_date_min, db_date_max] = get_min_max_dates(filtered_data);

        results = {
            'criteria': criteria,
            'items': filtered_data
        };
    };
    
    const clear_search = (e) => {
        results = {
            'criteria': false,
            'items': json_data
        };
    };


</script>

<div class="">
    <h2 class="text-2xl font-bold">Ferramenta de busca</h2>
    <Cabecalho 
        db_date_min='{db_date_min}' 
        db_date_max='{db_date_max}' 
        on:search-info={do_search} 
        on:clear={clear_search}
    />

    <Corpo {results}/>
</div>