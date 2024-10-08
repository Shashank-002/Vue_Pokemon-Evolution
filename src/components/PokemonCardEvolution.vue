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

    <!-- Evolution cards -->
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
import Card from './PokemonCardDetails.vue';

export default {
  name: 'PokemonCardEvolution',
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
        // For evolution, we are simply incrementing the ID for two evolutions
        const evolutionIds = [id + 1, id + 2];
        const evolutionData = await Promise.all(
          evolutionIds.map(evoId => this.fetchPokemonDetails(evoId))
        );

        this.evolutionCards = evolutionData.map(pokemon => ({
          id: pokemon.id,
          name: pokemon.name,
          image: pokemon.sprites.other["official-artwork"].front_default,
          types: pokemon.types.map(typeInfo => typeInfo.type.name),
        }));
      } catch (err) {
        console.error('Failed to fetch evolution:', err);
      }
    },

    async fetchPokemonDetails(id) {
      try {
        const response = await fetch(`https://pokeapi.co/api/v2/pokemon/${id}`);
        const data = await response.json();
        return data;
      } catch (error) {
        console.error('Error fetching Pokémon details:', error);
        return {};
      }
    },
  }
};
</script>

<style scoped>
img {
  width: 100%;
}

.pokemon-cards {
  background-color: white;
  border: 1px solid gray;
  width: auto;
  margin: 20px auto;
  display: flex;
  flex-wrap: wrap;
}

.evolution-cards {
  display: flex;
  flex-wrap: wrap;
  justify-content: left;
  width: 25%;
}

h2 {
  margin-top: 20px;
}

h1 {
  padding-left: 20px;
  color: red;
}
</style>
