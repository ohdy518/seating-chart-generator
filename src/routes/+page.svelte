<script>
    let width = 6;
    let height = 5;
</script>

<div class="flex flex-col gap-y-3 items-center justify-center w-screen h-screen inter">
    <div class="flex flex-row gap-x-2 items-center justify-center">
        <input id="w-input" class="border-2 px-2 py-1 w-16" placeholder="w" type="number" bind:value={width}> by
        <input id="h-input" class="border-2 px-2 py-1 w-16" placeholder="h" type="number" bind:value={height}>
    </div>
    <button class="border-2 px-8 py-1 font-bold" on:click={()=>{
        window.location.href = `/run?w=${width}&h=${height}`;
    }}>-> Go</button>
</div>