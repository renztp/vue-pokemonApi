<template>
  <div
    class="pokemon-modal"
    v-if="pokemonId"
    v-bind:style="{ background: pokemonInfo.info[pokemonId-1].color }"
  >
    <img class="pokemon-modal__pokeball" src="@/assets/pokeball.png" alt />
    <img class="pokemon-modal__close" v-on:click="toggleClose()" src="@/assets/close.png" />
    <div class="pokemon-modal__base-info">
      <div class="base-info__ident">
        <img
          v-bind:src="'https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/' + (pokemonId) + '.png'"
        />
        <p>{{ pokemonInfo.info[pokemonId-1].pokemon_name }}</p>
        <span>{{ pokemonInfo.info[pokemonId-1].species }}</span>
      </div>
      <div class="base-info__intro">
        <h4>Intro</h4>
        <p>{{ filterIntro[0].flavor_text }}</p>
      </div>
      <div v-if="dataCollection" class="base-info__stats">
        <div class="base-info__stats-container">
          <RadarChart :chart-data="dataCollection" :options="chartOptions" />
        </div>
      </div>
    </div>
    <!-- <ul>
      <li
        v-for="pokeStats in pokemonInfo.info[pokemonId-1].pokemon_stats"
        :key="pokeStats.id"
      >{{ pokeStats.base_stat }}</li>
    </ul>-->
    <!-- <div class v-if="dataCollection">
      <RadarChart :chart-data="dataCollection" :options="chartOptions" />
    </div>-->
  </div>
</template>

<script>
import RadarChart from "@/components/RadarChart.js";

export default {
  name: "PokemonSingle",
  components: {
    RadarChart
  },
  props: {
    isOpen: Boolean,
    pokemonId: Number,
    pokemonInfo: Object
  },
  data() {
    return {
      dataCollection: null,
      chartOptions: null
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
      return this.pokemonInfo.info[this.pokemonId - 1].intro.filter(bylang => {
        return bylang.language.name.match("en");
      });
    },
    arrayify: function() {
      return this.pokemonInfo.info[this.pokemonId - 1].pokemon_stats.map(
        x => x.base_stat
      );
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
        maintainAspectRatio: false,
        aspectRatio: 5,
        pointDot: false,
        showTooltips: false,
        scaleOverride: false,
        scaleSteps: 1,
        scaleStepWidth: 0,
        scaleStartValue: 0
      };
    }
  },
  mounted: function() {
    this.fillData();
  }
};
</script>


<style lang="scss" scoped>
.pokemon-modal {
  position: fixed;
  top: 48%;
  left: 50%;
  transform: translate(-50%, -50%);
  height: 670px;
  width: 426px;
  background: white;
  z-index: 9;
  display: flex;
  flex-wrap: wrap;
  color: #000;
  transform: translate(-50%, -50.1%);
  padding-top: 90px;

  &__pokeball {
    position: absolute;
    top: 30px;
    left: 80px;
    transform: rotate(30deg);
    z-index: 4;
    width: 100%;
    height: auto;
    width: 80px;
    opacity: 0.5;
  }

  &__close {
    position: absolute;
    top: 10px;
    right: 10px;
    width: 16px;
    cursor: pointer;
  }

  &__base-info {
    position: relative;
    z-index: 9;
    display: flex;
    flex-wrap: wrap;
    flex: 0 100%;
    justify-content: space-between;
    align-items: flex-start;
    align-content: flex-start;

    .base-info {
      &__ident {
        display: flex;
        flex: 0 100%;
        align-content: flex-start;
        flex-wrap: wrap;
        text-align: center;
        justify-content: center;
        padding: 10px 0 25px;
        background: white;
        border-top-right-radius: 10px;
        border-top-left-radius: 10px;
        box-shadow: 0px -1px 3px #6f3333;

        img {
          flex: 0 100%;
          height: 96px;
          width: 100%;
          max-width: 96px;
        }

        p {
          margin: 0;
          font-family: "Roboto";
          font-weight: 600;
          text-transform: capitalize;
          flex: 0 100%;
          margin-bottom: 3px;
        }

        span {
          font-size: 12px;
          font-weight: 700;
          flex: 0 100%;
          opacity: 0.6;
        }
      }

      &__stats {
        background: #fff;
        flex: 0 100%;
        justify-content: center;
        align-items: center;

        &-container {
          position: relative;
          width: 230px;
          margin: 0 auto;

          canvas {
            height: 290px !important;
          }
        }
      }

      &__intro {
        display: flex;
        flex: 0 100%;
        padding: 10px 20px 25px;
        flex-wrap: wrap;
        background: white;
        align-content: flex-start;
        align-items: flex-start;

        h4 {
          margin: 0;
          padding: 0;
        }
      }
    }
  }
}
</style>