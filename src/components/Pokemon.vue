<template>
  <div class="pokemon__item">
    <!-- {{ pokeId }} -->
    <span v-if="(pokemonData.id) < 10" class="pokemon__item__number">#00{{pokemonData.id}}</span>
    <span v-if="(pokemonData.id) >= 10" class="pokemon__item__number">#0{{pokemonData.id}}</span>
    <span v-if="(pokemonData.id) > 99">#{{pokemonData.id}}</span>
    <div class="pokemon__item__image">
      <img
        v-bind:src="'https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/' + (pokemonData.id) + '.png'"
      />
      <p>{{ pokemonData.pokemon_name }}</p>
      <span>{{ pokemonData.species }}</span>
    </div>
    <ul class="pokemon__item__stats">
      <li v-for="(stats, _id) in pokemonData.pokemon_stats" :key="_id">
        <span class="pokemon__item__stats__label">{{ pokemonStatsLabel[_id] }}</span>
        <span class="pokemon__item__stats__bar">
          <span v-if="stats.base_stat < 100" v-bind:style="{ width: stats.base_stat + '%'}"></span>
          <span v-else v-bind:style="{ width: '100%'}"></span>
        </span>
        <span class="pokemon__item__stats__stat">{{ stats.base_stat }}</span>
      </li>
    </ul>
  </div>
</template>

<script>
import { Bar } from "vue-chartjs";

export default {
  extends: Bar,
  name: "Pokemon",
  props: {
    pokemonData: Object,
    pokemonStatsLabel: Array,
    pokeId: Number
  }
};
</script>

<style lang="scss" scoped>
$palette-fighting: #c32;
$palette-flying: #bbf;
$palette-poison: #a4a;
$palette-ground: #ec7;
$palette-rock: #ca4;
$palette-bug: #cd6;
$palette-ghost: #86a;
$palette-steel: #79a;
$palette-fire: #f83;
$palette-water: #79f;
$palette-grass: #3c3;
$palette-electric: #ca5;
$palette-psychic: #f38;
$palette-ice: #aee;
$palette-dragon: #85f;
$palette-dark: #554;
$palette-fairy: #fae;
$palette-unknown: #666;
$palette-shadow: #777;
.pokemon__item {
  margin-bottom: 30px;
  flex: 0 45%;
  display: flex;
  position: relative;
  padding: 20px 10px;
  border-radius: 20px;
  /* background: #45f145; */
  box-shadow: 0px 0px 31px -3px #d6d6d6;
  background: #fff;
  cursor: pointer;
  transition: all 0.3s ease-in-out;

  &:hover {
    transform: translateY(-10px);
    box-shadow: 0px 17px 31px -3px #afafaf;
  }

  & > span {
    position: absolute;
    top: 10px;
    left: 10px;
    color: black;
    opacity: 0.5;
    font-weight: 600;
    letter-spacing: 0.04em;
  }

  &__image {
    flex: 0 48%;
    position: relative;
    text-align: center;

    p {
      margin: 0;
      font-family: "Roboto";
      font-weight: 600;
      text-transform: capitalize;
    }

    span {
      font-size: 12px;
      font-weight: 700;
      opacity: 0.6;
    }
  }

  &__stats {
    flex: 0 48%;
    padding: 0;
    margin: 0;
    list-style: none;
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;

    li {
      display: flex;
      flex: 0 100%;
      padding: 0;
      margin: 4px 0;
      width: 100%;
      align-items: center;
      justify-content: space-between;
    }

    span {
      display: inline-block;
    }

    &__label {
      flex: 0 27%;
      text-align: right;
      position: relative;
      right: 10px;
      font-size: 11px;
      letter-spacing: 0.04em;
      font-weight: 600;
    }

    &__bar {
      flex: 0 49%;
      position: relative;
      width: 40%;
      height: 10px;

      span {
        position: absolute;
        left: 0;
        top: 0;
        height: 100%;
        background: green;
        background-image: linear-gradient(90deg, green, rgb(116, 68, 5));
        border-radius: 2px;
      }
    }

    &__stat {
      flex: 0 24%;
      text-align: right;
      font-size: 11px;
      letter-spacing: 0.04em;
      font-weight: 600;
    }
  }
}

@media screen and (max-width: 425px) {
  .pokemon__item {
    flex: 0 100%;
  }
}
</style>