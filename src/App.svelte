<script lang="ts">
    import { fly } from 'svelte/transition';
    import { onMount } from 'svelte';
    import Pids from './lib/components/pids.svelte'
    import Config from './lib/components/config.svelte'
    let stnId: string;
    let platform: number;
    let showConfig: boolean = false;

    function getSavedData() {
        let savedPlat = localStorage.getItem("platform")
        let savedStn = localStorage.getItem("stnId");
        if(savedPlat == null || savedStn == null) {
            stnId = "1";
            platform = 2;
            showConfig = true;
        } else {
            stnId = savedStn;
            platform = parseInt(savedPlat);
        }
    }

    function saveData(stationId: string, platformNum: number) {
        if(stationId !== undefined) localStorage.setItem("stnId", stationId);
        if(platformNum !== undefined) localStorage.setItem("platform", platformNum.toString())
    }

    $: saveData(stnId, platform);

    function parseDataFromQuery() {
        const urlSearchParams = new URLSearchParams(window.location.search);
        const params = Object.fromEntries(urlSearchParams.entries());
        if(params["stnId"] != null && params["plat"] != null) {
            showConfig = false;
            stnId = params["stnId"];
            platform = parseInt(params["plat"]);
        }
    }

    onMount(() => {
        getSavedData();
        parseDataFromQuery();
    });
</script>

<main>
    
    <div class="portrait">
        <Pids {stnId} {platform} on:showConfig={() => showConfig = true}/>
    </div>

    {#if showConfig}
        <div transition:fly="{{ y:25, duration: 200 }}" class="portrait">
            <Config bind:stationId={stnId} bind:selectedPlatform={platform} on:click={() => showConfig = false}/>
        </div>
    {/if}
</main>

<style>
    main {
        position: absolute;
        width: 100%;
        height: 100%;
        background-color: #000;
        overflow-y: hidden;
    }

    .portrait {
        position: absolute;
        height: 100%;
        left: 0;
        right: 0;
        margin: 0 auto;
        aspect-ratio: 10 / 16;
    }
</style>