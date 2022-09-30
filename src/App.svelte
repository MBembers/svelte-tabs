<script>
  import Counter from './lib/Counter.svelte'
  import './lib/Tailwind.css'
  import {Router, Route, Link} from 'svelte-routing';
  import Home from './Home.svelte';
  import Board from './Board.svelte';
  import Header from './Header.svelte';
  import Footer from './Footer.svelte';
  import Api from './Api.svelte';
  import Pokemons from './Pokemons.svelte';
  import PokemonDetails from './PokemonDetails.svelte';
  import Wordle from './Wordle.svelte';

  let increment;
  let url = "";
</script>

<Header />
<section class="text-gray-600 body-font">
  <Router url="{url}">
    <Route path="/" component={Home} />
    <Route path="board" component={Board}/>
    <Route path="api" component={Api}/>
    <Route path="pokemon" component={Pokemons} />
    <Route path="pokemon/:id" component={PokemonDetails} />
    <Route path="wordle" component={Wordle} />

  </Router>
</section>

<Footer />