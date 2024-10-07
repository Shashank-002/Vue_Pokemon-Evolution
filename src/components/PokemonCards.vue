<template>
  <div class="pokemon-cards">
    <Card v-for="pokemon in pokemons" :key="pokemon.name" @click="fetchEvolution(pokemon.id)">
      <template v-slot:title>
        <h2>{{ pokemon.name }} #{{ pokemon.id }}</h2>
      </template>
      <template v-slot:content>
        <img :src="pokemon.image" :alt="pokemon.name" />
      </template>
      <template v-slot:footer>
        <div v-for="type in pokemon.types" :key="type">{{ type }}</div>
      </template>
    </Card>

    <!-- evolution card -->

    <div v-if="evolutionCards.length">
      <h1>Evolutions</h1>
      <div class="evolution-cards">
        <Card v-for="evo in evolutionCards" :key="evo.name">
          <template v-slot:title>
            <h2>{{ evo.name }} #{{ evo.id }}</h2>
          </template>
          <template v-slot:content>
            <img :src="evo.image" :alt="evo.name" />
          </template>
          <template v-slot:footer>
            <div v-for="type in evo.types" :key="type">{{ type }}</div>
          </template>
        </Card>
      </div>
    </div>
  </div>
</template>

<script>
import Card from './CardComponent.vue';

export default {
  name: 'PokemonCards',
  props: ['pokemons'],
  data() {
    return {
      evolutionCards: [],
    };
  },
  components: {
    Card,
  },
  methods: {
    async fetchEvolution(id) {
      try {
        const response = await fetch(`https://pokeapi.co/api/v2/evolution-chain/${id}`);
        const data = await response.json();
        this.evolutionCards = await this.extractEvolutionChain(data.chain);
      } catch (err) {
        console.error('Failed to fetch evolution:', err);
      }
    },

    async extractEvolutionChain(chain) {
      const evolutions = [];
      let current = chain;

      while (current) {
        const speciesId = current.species.url.split('/')[6];

        const pokemonDetails = await this.fetchPokemonDetails(speciesId);

        evolutions.push({
          id: pokemonDetails.id,
          name: current.species.name,
          image: `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/other/official-artwork/${speciesId}.png`,
          types: pokemonDetails.types.map(typeInfo => typeInfo.type.name),
        });

        current = current.evolves_to[0];
      }

      return evolutions;
    },

    async fetchPokemonDetails(speciesId) {
      try {
        const response = await fetch(`https://pokeapi.co/api/v2/pokemon/${speciesId}`);
        const data = await response.json();
        return data;
      } catch (error) {
        console.error('Error fetching Pokémon details:', error);
        return {};
      }
    }
  }

};
</script>

<style scoped>
img {
  width: 100%;
}

.evolution-cards {
  flex-wrap: wrap;
  justify-content: center;
}

h2 {
  margin-top: 20px;
}

h1 {
  padding-left: 20px;
  color: red;
}
</style>
