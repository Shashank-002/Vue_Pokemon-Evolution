<template>
  <div class="pokemon-cards">
    <Card v-for="pokemon in pokemons" :key="pokemon.name"
      :class="{ highlighted: selectedPokemonId === pokemon.id, blur: selectedPokemonId !== null && selectedPokemonId !== pokemon.id }"
      @click="handleCardClick(pokemon.id)">
      <template v-slot:title>
        <h2>{{ pokemon.name }} #{{ pokemon.id }}</h2>
      </template>
      <template v-slot:content>
        <img :src="pokemon.image" :alt="pokemon.name" />
      </template>
      <template v-slot:footer>
        <div class="type-list">
          <div v-for="type in pokemon.types" :key="type">{{ type }}</div>
        </div>
      </template>
    </Card>

    <!-- Loader -->
    <div v-if="loading" class="loader"></div>

    <!-- Evolution cards -->
    <div v-if="evolutionCards.length && !loading">
      <h1>Pokemon Evolutions</h1>
      <div class="evolution-cards">
        <Card v-for="evo in evolutionCards" :key="evo.name">
          <template v-slot:title>
            <h2>{{ evo.name }} #{{ evo.id }}</h2>
          </template>
          <template v-slot:content>
            <img :src="evo.image" :alt="evo.name" />
          </template>
          <template v-slot:footer>
            <div class="type-list">
              <div v-for="type in evo.types" :key="type">{{ type }}</div>
            </div>
          </template>
        </Card>
      </div>
    </div>
  </div>
</template>


<script>
import { ref } from 'vue';
import Card from './PokemonCardDetails.vue';

export default {
  name: 'PokemonCardEvolution',
  props: ['pokemons'],
  components: {
    Card,
  },
  setup() {
    const evolutionCards = ref([]);
    const loading = ref(false);
    const selectedPokemonId = ref(null);

    const fetchEvolution = async (id) => {
      try {
        loading.value = true;
        await new Promise(resolve => setTimeout(resolve, 500));
        const evolutionIds = [id + 1, id + 2];
        const evolutionData = await Promise.all(
          evolutionIds.map(evoId => fetchPokemonDetails(evoId))
        );

        evolutionCards.value = evolutionData.map(pokemon => ({
          id: pokemon.id,
          name: pokemon.name,
          image: pokemon.sprites.other['official-artwork'].front_default,
          types: pokemon.types.map(typeInfo => typeInfo.type.name),
        }));
      } catch (err) {
        console.error('Failed to fetch evolution:', err);
      } finally {
        loading.value = false;
      }
    };

    const fetchPokemonDetails = async (id) => {
      try {
        const response = await fetch(`https://pokeapi.co/api/v2/pokemon/${id}`);
        const data = await response.json();
        return data;
      } catch (error) {
        console.error('Error fetching Pokémon details:', error);
        return {};
      }
    };

    const handleCardClick = (id) => {
      selectedPokemonId.value = id;
      fetchEvolution(id);
    };

    return {
      evolutionCards,
      loading,
      selectedPokemonId,
      handleCardClick,
    };
  },
};
</script>

<style scoped>
img {
  width: 100%;
}

/* Style for the loader */
.loader {
  border: 16px solid #f3f3f3;
  border-radius: 50%;
  border-top: 16px solid #3498db;
  width: 80px;
  height: 80px;
  animation: spin 2s linear infinite;
  margin: 20px auto;
  display: block;
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }

  100% {
    transform: rotate(360deg);
  }
}

.pokemon-cards {
  background-color: white;
  border: 1px solid gray;
  margin: 20px auto;
}

.evolution-cards {
  display: flex;
  flex-wrap: wrap;
  justify-content: left;
}

.type-list {
  display: flex;
  flex-direction: column;
  align-items: center;
}

h2 {
  margin-top: 20px;
}

h1 {
  padding-left: 20px;
  color: red;
}

.blur {
  filter: blur(2px);
  transition: filter 0.3s ease;
}
</style>
