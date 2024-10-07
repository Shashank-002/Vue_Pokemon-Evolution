<template>
  <div>
    <PokemonCards :pokemons="pokemons" />
  </div>
</template>

<script>
import PokemonCards from './components/PokemonCards.vue';

export default {
  data() {
    return {
      pokemons: [],
    };
  },
  components: {
    PokemonCards,
  },
  mounted() {
    const urls = [
      "https://pokeapi.co/api/v2/pokemon/1",
      "https://pokeapi.co/api/v2/pokemon/4",
      "https://pokeapi.co/api/v2/pokemon/7",
    ];

    Promise.all(urls.map(url => fetch(url).then(res => res.json())))
      .then(data => {
        this.pokemons = data.map(pokemon => ({
          id: pokemon.id,
          name: pokemon.name,
          image: pokemon.sprites.other["official-artwork"].front_default,
          types: pokemon.types.map(t => t.type.name),
        }));
      });
  },
};
</script>
