<script>
  import { each } from "svelte/internal";
  let searchFilter;

    async function fetchPokemons(searchFilter){
      const response = await fetch("https://pokeapi.co/api/v2/pokemon/?limit=100&offset=0");
      const data = await response.json();
      
      if (response.ok) {

        if(!searchFilter) return data.results;

        let sussy = data.results.filter(p => p.name.includes(searchFilter));
        console.log(sussy);
        return sussy;
      } else {
        throw new Error(data);
      }
    }
  </script>
  
  <input type="text" placeholder="name..." name="filter" id="filter" style="border: 2px solid black; margin-left: 20px"
  bind:value={searchFilter}>
  <div style="display: flex; flex-direction: row; flex-wrap: wrap; width: 100vw">
    
    {#await fetchPokemons(searchFilter)}
    <p>...waiting</p>
  {:then data}
  {#each data as pokemon}

          <div class="m-2 p-5">
            <div class="bg-gray-900 bg-opacity-75 rounded-lg overflow-hidden text-center relative">
              <h1 class="title-font sm:text-2md font-medium text-white">
                <a href="/pokemon/{pokemon.url.split("/")[6]}">
                {pokemon.name}
              </a>
              </h1> 
              <img src="https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/{pokemon.url.split("/")[6]}.png" alt="this is {pokemon.name}">
        
            </div>
          </div>
          
    {/each}
  {/await}
  </div>
  