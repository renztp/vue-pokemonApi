<template>
  <div class="pokemon-modal" v-if="pokemonId">
    <button class="pokemon-modal__close" v-on:click="toggleClose()">< Pokemons</button>
    <div class="pokemon-modal__wrapper01">
      <!-- POKEMON NAME AND SPRITE -->
      <div class="pokemon-modal__info01" v-bind:style="pokeColor">
        <div class="pokemon-modal__name">
          <span class="pokemon-modal__name-id">{{ pokeId }}</span>
          <p class="pokemon-modal__name-name">{{ pokeName }}</p>
          <span class="pokemon-modal__name-type">{{ pokeType }}</span>
        </div>
        <div class="pokemon-modal__sprites">
          <a v-bind:href="'http://pokemondb.net/pokedex/' + pokeName" target="_blank">
            <img
              v-bind:src="'https://img.pokemondb.net/sprites/black-white/anim/normal/' + pokeName + '.gif'"
              alt
            />
          </a>
        </div>
      </div>
      <!-- POKERMON BIO -->
      <div class="pokemon-modal__info02">
        <h4>Intro</h4>
        <p>{{ pokeBio }}</p>
      </div>
      <div class="pokemon-modal__info03">
        <h4>Evolution Chain</h4>
        <div class="pokemon-modal__chain">
          <div v-for="(evo, i) in evolution" :key="i" class="pokemon-modal__chain-item">
            <a v-bind:href="'http://pokemondb.net/pokedex/' + evo.species_name" target="_blank">
              <img
                v-bind:src="'https://img.pokemondb.net/sprites/black-white/anim/normal/' + evo.species_name + '.gif'"
                alt
              />
            </a>
            <p>{{ evo.species_name }}</p>
          </div>
        </div>
      </div>
    </div>
    <div class="pokemon-modal__wrapper02">
      <div class="pokemon-modal__info04">
        <h4>Basic Info</h4>
        <div class="pokemon-modal__basic-item">
          <span>Height</span>
          <p>{{ pokeHeight }}ft</p>
        </div>
        <div class="pokemon-modal__basic-item">
          <span>Element</span>
          <ul>
            <li v-for="(elm, elmKey) in pokeElement" :key="elmKey">{{ elm }}</li>
          </ul>
        </div>
        <div class="pokemon-modal__basic-item">
          <span>Weight</span>
          <p>{{ pokeWeight }}kg</p>
        </div>
      </div>
      <div class="pokemon-modal__info05">
        <h4>Stats</h4>
        <div class="pokemon-modal__chart">
          <RadarChart :chart-data="dataCollection" :options="chartOptions" />
        </div>
      </div>
    </div>
  </div>
</template>


<script>
import RadarChart from "@/components/RadarChart.js";
import axios from "axios";

export default {
  name: "PokemonSingle",
  components: {
    RadarChart
  },
  props: {
    isOpen: Boolean,
    pokemonId: Number,
    pokemonInfo: Object,
    pokemonEvoData: Array
  },
  data() {
    return {
      dataCollection: null,
      chartOptions: null,
      evolution: [],
      currPokeInfo: this.pokemonInfo.info[this.pokemonId - 1]
    };
  },
  computed: {
    dataLabels: function() {
      return this.pokemonInfo.stats_label.map(label => label);
    },
    dataStats: function() {
      return this.pokemonInfo.info.pokemon_stats;
    },
    filterIntro: function() {
      return this.currPokeInfo.intro.filter(bylang => {
        return bylang.language.name.match("en");
      });
    },
    arrayify: function() {
      return this.currPokeInfo.pokemon_stats.map(x => x.base_stat);
    },
    pokeName: function() {
      return this.currPokeInfo.pokemon_name;
    },
    pokeType: function() {
      return this.currPokeInfo.species;
    },
    pokeBio: function() {
      return this.currPokeInfo.intro.flavor_text;
    },
    pokeEvoId: function() {
      return this.currPokeInfo.evolution_id;
    },
    pokeElement: function() {
      return this.currPokeInfo.types;
    },
    pokeColor: function() {
      let theColor = this.currPokeInfo.color;
      // return this.pokemonInfo.info[this.pokemonId - 1].color;
      if (theColor == "yellow") {
        return { background: theColor, color: "black" };
      } else if (theColor == "white") {
        return { background: "rgb(187, 187, 255)", color: "black" };
      } else {
        return { background: theColor, color: "white" };
      }
    },
    pokeHeight: function() {
      return (
        Math.round(
          (this.currPokeInfo.pokemon_height * 0.328084 + 0.0001) * 100
        ) / 100
      );
    },
    pokeWeight: function() {
      return (
        Math.round(
          (this.currPokeInfo.pokemon_weight * 0.220462 + 0.0001) * 100
        ) / 100
      );
    },
    pokeId: function() {
      if (this.pokemonId < 10) {
        return `#00${this.pokemonId}`;
      } else if (this.pokemonId >= 10 && this.pokemonId <= 99) {
        return `#0${this.pokemonId}`;
      } else if (this.pokemonId > 99) {
        return `#${this.pokemonId}`;
      }
    }
  },
  methods: {
    toggleClose: function() {
      this.$emit("toggleClose", false);
    },
    fillData: function() {
      this.dataCollection = {
        labels: this.dataLabels,
        datasets: [
          {
            label: "",
            borderColor: "rgba(0, 0, 0, 0.5)",
            borderWidth: 2,
            data: this.arrayify
          }
        ]
      };
      this.chartOptions = {
        scale: {
          pointLabels: {
            fontSize: 10
          }
        },
        legend: {
          display: false
        },
        title: {
          display: true,
          text: ""
        },
        responsive: true,
        maintainAspectRatio: false
        // aspectRatio: 2
        // pointDot: false,
        // showTooltips: false,
        // scaleOverride: false,
        // scaleSteps: 1,
        // scaleStepWidth: 0,
        // scaleStartValue: 0
      };
    }
  },
  mounted: function() {
    this.fillData();
    axios.get(this.pokeEvoId).then(res => {
      console.log(res.data.chain);
      let evoData = res.data.chain;
      do {
        var evoDetails = evoData["evolution_details"][0];
        this.evolution.push({
          species_name: evoData.species.name,
          min_level: !evoDetails ? 1 : evoDetails.min_level,
          trigger_name: !evoDetails ? null : evoDetails.trigger.name,
          item: !evoDetails ? null : evoDetails.item
        });
        evoData = evoData["evolves_to"][0];
      } while (!!evoData && evoData.hasOwnProperty("evolves_to"));
    });
  }
};
</script>


<style lang="scss">
.pokemon-modal {
  position: fixed;
  display: flex;
  justify-content: space-between;
  padding: 30px 15px 30px;
  box-sizing: border-box;
  top: 48%;
  left: 50%;
  transform: translate(-50%, -50%);
  // height: 570px;
  width: 662px;
  background: white;
  z-index: 9;
  transform: translate(-50%, -50.1%);
  border-radius: 15px;
  color: #000;

  h4 {
    margin: 0 0 10px;
  }

  &__wrapper01 {
    display: flex;
    flex-wrap: wrap;
    align-content: flex-start;
    flex: 0 49%;
  }

  &__close {
    position: absolute;
    top: 7px;
    left: 10px;
    height: 20px;
    display: inline;
    background: transparent;
    border: 0;
    font-size: 12px;
    cursor: pointer;
  }

  &__info01 {
    display: flex;
    flex: 100%;
    box-sizing: border-box;
    justify-content: space-between;
    align-items: center;
    padding: 25px 15px;
    margin-bottom: 30px;
    border-radius: 15px;
    color: white;
    letter-spacing: 0.03em;
    font-weight: 500;
  }

  &__name {
    text-transform: capitalize;
    flex: 0 50%;
    letter-spacing: 0.03em;

    &-id {
      font-size: 12px;
    }

    &-name {
      margin: 0;
      font-size: 22px;
      margin: 0;
    }

    &-type {
      font-size: 13px;
      opacity: 0.8;
    }
  }

  &__sprites {
    flex: 0 50%;
    text-align: center;
  }

  &__info02 {
    width: 100%;
    margin-bottom: 20px;
    letter-spacing: 0.03em;
    line-height: 1.3;

    p {
      margin: 0;
      font-size: 14px;
    }
  }

  &__chain {
    display: flex;
    flex-wrap: wrap;
  }

  &__chain-item {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-items: flex-start;
    align-items: center;
    border-radius: 15px;
    padding: 5px 15px;
    min-height: 90px;
    margin-bottom: 10px;
    flex: 1;
    a {
      flex: 40%;
      display: flex;
      align-items: center;
      margin-bottom: 15px;
    }
    p {
      font-size: 16px;
      text-transform: capitalize;
    }
  }

  &__wrapper02 {
    display: flex;
    flex-wrap: wrap;
    align-content: flex-start;
    flex: 0 49%;
  }

  &__info04 {
    display: flex;
    width: 100%;
    margin-bottom: 30px;
    justify-content: space-between;
    flex-wrap: wrap;
    align-content: flex-start;

    h4 {
      flex: 0 100%;
    }
  }

  &__basic-item {
    position: relative;
    flex: 0 32.33%;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 80px;
    background: #ccc;

    span {
      position: absolute;
      bottom: 8px;
      left: 50%;
      transform: translateX(-50%);
      font-size: 10px;
      color: #666;
    }

    p {
      font-size: 16px;
      color: #000;
    }

    ul {
      padding: 0;
      margin: 0;

      li {
        list-style: none;
        text-align: center;
        font-size: 16px;
        text-transform: capitalize;
        color: #000;
      }
    }
  }

  &__info05 {
    width: 100%;
  }

  &__chart {
    width: 260px;
    margin: -60px auto;
  }
}

@media screen and (max-width: 425px) {
  .pokemon-modal {
    width: 100%;
    height: 100%;
    overflow-y: scroll;
    flex-wrap: wrap;
    transform: unset;
    top: 0;
    left: unset;
    border-radius: 0;

    &__wrapper01 {
      flex: 0 100%;
    }

    &__wrapper02 {
      flex: 0 100%;
    }
  }
}
</style>