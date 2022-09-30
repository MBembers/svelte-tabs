<script>
  import { each } from "svelte/internal";
  export let id;
  console.log(id);

  async function fetchPokemon(){
      const response = await fetch("https://pokeapi.co/api/v2/pokemon/"+id);
      const data = await response.json();
      
      console.log(data)
  
      if (response.ok) {
        return data;
      } else {
        throw new Error(data);
      }
    }
  
    let promise = fetchPokemon();
  
</script>

{#await promise}
<p>...waiting</p>
{:then pokemon}
  <div>
    <div class="m-2 p-5 text-white" style="max-width: 500px;">
      <div class="bg-gray-900 bg-opacity-75 rounded-lg overflow-hidden text-center relative" style="display: flex; flex-direction: column; align-items:center">
        <h1 class="title-font sm:text-2md font-medium text-white">
          {pokemon.name}
        </h1> 
        <img src="https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/{id}.png" alt="this is {pokemon.name}" width="300px">
        
        <p>id: {id}</p>
        <p>height: {pokemon.height}</p>
        <h2>Stats:  </h2>
        {#each pokemon.stats as stat}
          <p>{stat.stat.name}: {stat.base_stat}</p>
        {/each}

      </div>
    </div>
  </div>
{/await}