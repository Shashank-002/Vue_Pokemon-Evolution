<template>
  <div>
    <PokemonCardDetails :pokemons="pokemons" />
  </div>
</template>

<script>
import { ref, onMounted } from 'vue';
import PokemonCardDetails from './components/PokemonCardDetails.vue';

export default {
  components: {
    PokemonCardDetails,
  },
  setup() {
    const pokemons = ref([]);

    const fetchPokemons = async () => {
      const urls = [
        "https://pokeapi.co/api/v2/pokemon/1",
        "https://pokeapi.co/api/v2/pokemon/4",
        "https://pokeapi.co/api/v2/pokemon/7",
      ];

      const data = await Promise.all(urls.map(url => fetch(url).then(res => res.json())));
      pokemons.value = data.map(pokemon => ({
        id: pokemon.id,
        name: pokemon.name,
        image: pokemon.sprites.other["official-artwork"].front_default,
        types: pokemon.types.map(t => t.type.name),
      }));
    };

    onMounted(() => {
      fetchPokemons();
    });

    return {
      pokemons,
    };
  },
};
</script>
