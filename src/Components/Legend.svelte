<script>
  export let palette;
  export let single;
  export let secondgrowth;
  export let map_palette_single;
  export let map_palette_planned;

  let legend_palette = [
    { value: "0", color: palette[0] },
    { value: "15", color: palette[15] },
    { value: "30", color: palette[30] },
    { value: "45", color: palette[45] },
    { value: ">60", color: palette[60] },
    { value: "Planned", color: map_palette_planned[1]}
  ];

  let legend_single = [
    { value: "Harvested", color: map_palette_single }
  ];

  let legend_planned = [
    {value: "Planned", color: map_palette_planned[1]}
  ];
</script>

<style>
  .legend {
    @apply text-gray-400 p-2 z-50 text-xs;
  }

  .legendspan {
    @apply inline-block h-2 mr-1 w-3 text-xs;
  }
</style>

{#if single}
  <div class="legend">
    {#each legend_single as { value, color }}
      <div>
        <span class="legendspan" style="background-color: {color}" />
        {value}
      </div>
    {/each}
  </div>
{:else}
  <div class="legend">
    <div class="text-xs">
      Years Since
      <br />
      Harvested
    </div>
    {#each legend_palette as { value, color }}
      <div>
        <span class="legendspan" style="background-color: {color}" />
        {value}
      </div>
    {/each}
  </div>
{/if}

