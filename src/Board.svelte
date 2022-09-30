<script>
  let selectedX, selectedY;
  let lista = [1,2,3,4,5]
</script>

<div style="margin-left: 50px;">

<select bind:value={selectedX}>
  {#each lista as item}
    <option value="{item}">{item}</option>
  {/each}
</select>
<p>wybrano: {selectedX}</p>

<div style="display: flex; flex-directin: row; flex-wrap: wrap; max-width: {selectedX * 100}px">
  {#each Array(selectedX) as x}
    {#each Array(selectedX) as x}
      <img src="./src/assets/svelte.png" alt="svelte" width="100px" height="100px" />
    {/each}
  {/each}
</div>
</div>
