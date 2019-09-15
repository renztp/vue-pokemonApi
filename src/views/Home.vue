<template>
  <div class="home">
    <Search v-on:updateSearch="emitSearch($event)" />
    <ul class="pokemon__container" v-if="pokemonData.data_loaded && pokemonData.stats_loaded">
      <Pokemon
        v-for="(pokemon, i) in filterPokemon"
        :key="i"
        v-bind:pokemonData="pokemon"
        v-bind:pokemonStatsLabel="pokemonData.stats_label"
      />
    </ul>
  </div>
</template>

<script>
// @ is an alias to /src
import axios from "axios";
import Pokemon from "@/components/Pokemon.vue";
import Search from "@/components/Search.vue";

export default {
  name: "home",
  components: {
    Pokemon,
    Search
  },
  data() {
    return {
      pokemonData: {
        pokemonSearch: "",
        info: [],
        stats_label: ["SPD", "S.DEF", "S.ATK", "DEF", "ATK", "HP"],
        limit: 12,
        data_loaded: false,
        stats_loaded: false
      }
    };
  },
  methods: {
    emitSearch: function(updatedSearch) {
      this.pokemonData.pokemonSearch = updatedSearch.toLowerCase();
    }
  },
  computed: {
    filterPokemon: function() {
      if (this.pokemonData.pokemonSearch.length > 0) {
        return this.pokemonData.info.filter(byName => {
          return byName.pokemon_name.match(this.pokemonData.pokemonSearch);
        });
      } else {
        return this.pokemonData.info;
      }
    }
  },
  created() {
    const config = {
      headers: {
        "Content-type": "application/json"
      }
    };
    this.pokemonData.data_loaded = false;
    this.pokemonData.stats_loaded = false;
    for (var iter = 1; iter <= this.pokemonData.limit; iter++) {
      axios
        .all([
          axios.get(`https://pokeapi.co/api/v2/pokemon/${iter}`, config),
          axios.get(`https://pokeapi.co/api/v2/pokemon-species/${iter}`, config)
        ])
        .then(
          axios.spread((dataRes, speciesRes) => {
            this.pokemonData.info.push({
              id: dataRes.data.id,
              pokemon_name: dataRes.data.name,
              species: speciesRes.data.genera[2].genus,
              pokemon_stats: dataRes.data.stats
            });
          })
        )
        .then(async () => {
          this.pokemonData.data_loaded = true;
          this.pokemonData.stats_loaded = true;
        });
    }
  }
};
</script>
<style lang="scss">
.home {
  width: 100%;
  max-width: 1200px;
  margin: 0 auto;
}

.pokemon {
  &__container {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;
  }
}

@media screen and (max-width: 425px) {
  .pokemon {
    &__container {
      padding: 0 15px;
    }
  }
}
</style>