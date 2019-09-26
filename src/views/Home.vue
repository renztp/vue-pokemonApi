<template>
  <div class="home" v-bind:class="{ 'opened-modal': modalOpened }">
    <Search v-on:updateSearch="emitSearch($event)" />
    <ul class="pokemon__container" v-if="pokemonData.data_loaded && pokemonData.stats_loaded">
      <Pokemon
        v-for="(pokemon, i) in filterPokemon"
        :key="i"
        v-bind:pokemonData="pokemon"
        v-bind:pokemonStatsLabel="pokemonData.stats_label"
        @click.native="processPokemonInfo(pokemon.id)"
        v-bind:pokeId="pokemon.id"
      />
    </ul>
    <div v-if="modalOpened && pokemonData.data_loaded && misc_id">
      <PokemonSingle
        v-bind:isOpen="modalOpened"
        v-on:toggleClose="emitClose($event)"
        v-bind:pokemonId="misc_id"
        v-bind:pokemonInfo="pokemonData"
      />
    </div>
    <div class="loadmore-container">
      <button
        class="loadmore-pokemon"
        @click="loadMore()"
        v-if="pokemonData.data_loaded != false"
      >Load more pokemon</button>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import axios from "axios";
import Pokemon from "@/components/Pokemon.vue";
import Search from "@/components/Search.vue";
import PokemonSingle from "@/components/PokemonSingle.vue";

export default {
  name: "home",
  components: {
    Pokemon,
    Search,
    PokemonSingle
  },
  data() {
    return {
      modalOpened: false,
      pokemonData: {
        pokemonSearch: "",
        info: [],
        stats_label: ["SPD", "S.DEF", "S.ATK", "DEF", "ATK", "HP"],
        limit: 12,
        data_loaded: false,
        stats_loaded: false
      },
      misc_id: null
    };
  },
  methods: {
    emitSearch: function(updatedSearch) {
      this.pokemonData.pokemonSearch = updatedSearch.toLowerCase();
    },
    emitClose: function(closedModal) {
      this.modalOpened = closedModal;
    },
    processPokemonInfo: function(pokeId) {
      this.modalOpened = true;
      this.misc_id = pokeId;
    },
    loadMore: async function() {
      const config = {
        headers: {
          "Content-type": "application/json"
        }
      };
      for (
        var callIter = this.pokemonData.limit + 1;
        callIter <= this.pokemonData.limit + 12;
        callIter++
      ) {
        axios
          .all([
            await axios.get(
              `https://pokeapi.co/api/v2/pokemon/${callIter}`,
              config
            ),
            await axios.get(
              `https://pokeapi.co/api/v2/pokemon-species/${callIter}`,
              config
            ),
            await axios.get(
              `https://pokeapi.co/api/v2/evolution-chain/${callIter}`,
              config
            )
          ])
          .then(
            await axios.spread((dataRes, speciesRes, evoRes) => {
              this.pokemonData.info.push({
                id: dataRes.data.id,
                pokemon_name: dataRes.data.name,
                species: speciesRes.data.genera[2].genus,
                habitat: speciesRes.data.habitat.name,
                color: speciesRes.data.color.name,
                intro: speciesRes.data.flavor_text_entries,
                pokemon_stats: dataRes.data.stats,
                evolution_chain: evoRes.data.chain.evolves_to[0]
              });
              console.log("loading...");
            })
          );
        // .then(() => {
        //   console.log(
        //     this.pokemonData.info.filter(byId => {
        //       let arr = byId.id;
        //     })
        //   );
        // });
      }

      this.pokemonData.limit += 12;
      console.log("LOADED");
      // console.log(`Current Limit: ${this.pokemonData.limit}`);
    }
  },
  computed: {
    // TODO: Add more filters.
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
  async created() {
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
          await axios.get(`https://pokeapi.co/api/v2/pokemon/${iter}`, config),
          await axios.get(
            `https://pokeapi.co/api/v2/pokemon-species/${iter}`,
            config
          ),
          axios.get(`https://pokeapi.co/api/v2/evolution-chain/${iter}`, config)
        ])
        .then(
          axios.spread((dataRes, speciesRes, evoRes) => {
            this.pokemonData.info.push({
              id: dataRes.data.id,
              pokemon_name: dataRes.data.name,
              species: speciesRes.data.genera[2].genus,
              habitat: speciesRes.data.habitat.name,
              color: speciesRes.data.color.name,
              intro: speciesRes.data.flavor_text_entries,
              pokemon_stats: dataRes.data.stats,
              evolution_chain: evoRes.data.chain.evolves_to[0]
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
  margin: 0 auto 30px;
}

.opened-modal {
  &:before {
    content: "";
    position: fixed;
    left: 0;
    top: 0;
    height: 100%;
    width: 100%;
    background: rgba(0, 0, 0, 0.5);
    z-index: 5;
  }
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

.loadmore-container {
  box-sizing: border-box;
}

.loadmore-pokemon {
  border: 0;
  display: block;
  width: 100%;
  max-width: 200px;
  text-align: center;
  padding: 15px 0;
  cursor: pointer;
  outline: 0;
  background: #cd5241;
  color: white;
  border-radius: 10px;
}

@media screen and (max-width: 425px) {
  .pokemon {
    &__container {
      padding: 0 15px;
    }
  }

  .loadmore-container {
    padding: 0 15px;
  }
}
</style>
