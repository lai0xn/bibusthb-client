<script>
    export let faculty;
    faculty.groups = faculty.modules?.reduce((acc, module) => {
        if(!acc.some(group => group.id === module.group.id)) acc.push(module.group);
        return acc;
    }, [])

    let selected_group_id = -1;
    let selected_module_id = -1;
    let select_group = (id) => {
        selected_module_id = -1;
        if(selected_group_id === id) selected_group_id = -1;
        else selected_group_id = id;
    };
    let select_module = (id) => {
        if(selected_module_id === id) selected_module_id = -1;
        else selected_module_id = id;
    }

    $: modules = faculty.modules.filter(m=>m.group.id===selected_group_id);
    import {load} from './helper.js';
</script>

<main id="{faculty.short}-page">
    <h2>{faculty.name}&nbsp;</h2>
    <div id="navigator">
        <div class="collumn">
            {#each faculty.groups as group}
                <!-- svelte-ignore a11y-click-events-have-key-events -->
                <div class="item {group.id==selected_group_id}" on:click={()=>select_group(group.id)}>
                    <a href="https://drive.google.com/drive/folders/{group.drive_id}" target="_blank">
                        {group.name}
                    </a>
                </div>
            {/each}
        </div>
        <hr>
        <div class="collumn">
            {#each modules as module}
                <!-- svelte-ignore a11y-click-events-have-key-events -->
                <div class="item {module.id==selected_module_id}" on:click={()=>select_module(module.id)}>
                    <a href="https://drive.google.com/drive/folders/{module.drive_id}" target="_blank">
                        {module.short}
                    </a>
                    {#await load(`https://walrus-app-mwr59.ondigitalocean.app/api/module/${module.id}/getcount/`)}
                        0
                    {:then counts}
                        <span>{
                        counts.cour_count + 
                        counts.exam_count +
                        counts.td_count +
                        counts.tp_count +
                        counts.other_count                            
                        }</span>
                    {/await}
                </div>
            {/each}
        </div>
        <hr>
        <div class="collumn">
            {#if selected_module_id !== -1}
            {#await load(`https://walrus-app-mwr59.ondigitalocean.app/api/module/${selected_module_id}/getcount/`) then counts}
                <a class="item" href="https://drive.google.com/drive/folders/{faculty.modules.find(mod=>mod.id===selected_module_id).cour_drive_id}" target="_blank">
                    lessons
                    <span>{counts.cour_count}</span>
                </a>
                <a class="item" href="https://drive.google.com/drive/folders/{faculty.modules.find(mod=>mod.id===selected_module_id).exam_drive_id}" target="_blank">
                    exams
                    <span>{counts.exam_count}</span>
                </a>
                <a class="item" href="https://drive.google.com/drive/folders/{faculty.modules.find(mod=>mod.id===selected_module_id).td_drive_id}" target="_blank">
                    directed work (TD)
                    <span>{counts.td_count}</span>
                </a>
                <a class="item" href="https://drive.google.com/drive/folders/{faculty.modules.find(mod=>mod.id===selected_module_id).tp_drive_id}" target="_blank">
                    practical work (TP)
                    <span>{counts.tp_count}</span>
                </a>
                <a class="item" href="https://drive.google.com/drive/folders/{faculty.modules.find(mod=>mod.id===selected_module_id).other_drive_id}" target="_blank">
                    other
                    <span>{counts.other_count}</span>
                </a>
            {/await}
            {/if}
        </div>
    </div>
</main>

<style>
    main{
        min-height: 200px;
        display: grid;
        grid-template-rows: auto 1fr;
    }
    h2{
        margin: 10px 0px 0px 20px;
        text-decoration: var(--brand-green) underline;
        user-select: none;
        cursor: pointer;
        width: fit-content;
        font-family: 'Rubik';
        color: #101010;
    }
    #navigator{
        margin: 30px var(--side-margin) 50px var(--side-margin);
        padding: 10px;
        padding-bottom: 20px;
        display: grid;
        grid-template-columns: 5fr 3fr 5fr;
        background-color: var(--off-white);
        box-shadow: var(--window-shadow);
        gap: 10px;
        border-radius: 10px;
        font-weight: 500;
    }
    hr{
        display: none;
    }
    @media (max-width: 700px), (orientation: portrait) {
        #navigator{
            grid-template-columns: 1fr;
            gap: 20px;
        }
        hr{
            display: unset;
            background-color: #666;
            height: 1px;
            width: 80%;
            margin: 0px auto;
        }
    }
    .collumn{
        display: flex;
        flex-direction: column;
        gap: 8px;
    }
    .item{
        background-color: var(--off-off-white);
        color: black;
        text-decoration: none;
        padding: 17px 17px 17px 17px;
        display: flex;
        justify-content: space-between;
        border-radius: 10px;
        user-select: none;
        cursor: pointer;
        transition: background-color 0.2s ease-out;
        animation: apear 0.2s ease-in-out;
        box-shadow: #0001 0 3px 6px;
    }
    .true{
        background-color: var(--light-green);
    }
    @keyframes apear{
        from{ opacity: 0; }
        to{ opacity: 1; }
    }
    .item:hover{
        background-color:var(--brand-green)

}
    /*.item span{
        color: var(--brand-green);
        text-decoration: underline;
    }*/
    a{
        color: black;
        text-decoration: none;
    }
    a:hover{

    }
</style>
