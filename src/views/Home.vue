<template>
  <div class="home">
    <ul class="pokemon__container" v-if="pokemonData.data_loaded && pokemonData.stats_loaded">
      <Pokemon
        v-for="(pokemon, _id) in pokemonData.names"
        :key="_id"
        v-bind:pokemonName="pokemon"
        v-bind:pokemonStats="pokemonData.stats[_id]"
        v-bind:pokemonId="_id"
        v-bind:pokemonStatsLabel="pokemonData.stats_label"
        v-bind:pokemonOtherData="pokemonData.other_data[_id]"
        v-bind:pokemonSpecies="pokemonData.species"
      />
    </ul>
  </div>
</template>

<script>
// @ is an alias to /src
import axios from "axios";
import Pokemon from "@/components/Pokemon.vue";

export default {
  name: "home",
  components: {
    Pokemon
  },
  data() {
    return {
      pokemonData: {
        names: [],
        stats: [],
        species: [],
        other_data: [],
        stats_label: ["SPD", "S.DEF", "S.ATK", "DEF", "ATK", "HP"],
        limit: 12,
        data_loaded: false,
        stats_loaded: false
      }
    };
  },
  created() {
    const config = {
      headers: {
        "Content-type": "application/json"
      }
    };
    this.pokemonData.data_loaded = false;
    this.pokemonData.stats_loaded = false;
    axios
      .get(
        `https://pokeapi.co/api/v2/pokemon?limit=${this.pokemonData.limit}`,
        config
      )
      .then(res => {
        this.pokemonData.names = res.data.results;
        // console.log(this.pokemonData.names);
        this.pokemonData.data_loaded = true;
      })
      .then(async () => {
        if (this.pokemonData.data_loaded) {
          for (var iter = 1; iter <= this.pokemonData.limit; iter++) {
            // await axios
            //   .get(`https://pokeapi.co/api/v2/pokemon/${iter}`, config)
            //   .then(res => {
            //     this.pokemonData.other_data.push(res.data.types);
            //     this.pokemonData.stats.push(res.data.stats);
            //     console.log(this.pokemonData.other_data);
            //     this.pokemonData.stats_loaded = true;
            //   });
            await axios
              .all([
                axios.get(`https://pokeapi.co/api/v2/pokemon/${iter}`),
                axios.get(`https://pokeapi.co/api/v2/pokemon-species/${iter}`)
              ])
              .then(
                axios.spread((dataRes, speciesRes) => {
                  // Pokemon Data Response 👇
                  this.pokemonData.other_data.push(dataRes.data.types);
                  this.pokemonData.stats.push(dataRes.data.stats);
                  this.pokemonData.stats_loaded = true;

                  // Pokemon Species Response 👇
                  this.pokemonData.species.push(
                    speciesRes.data.genera[2].genus
                  );
                })
              );
          }
          setTimeout(() => {}, 1000);
        }
      });
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