<template>
  <div class="filter" v-if="typesLoaded">
    <div class="filter__container">
      <div class="filter__inner">
        <carousel :perPageCustom="[[320, 3],[425,6],[768, 7],[1024, 8]]" :paginationEnabled="false">
          <slide v-for="(type, i) in types" :key="i">
            <div class="filter__type-container">
              <button v-bind:style="{ backgroundColor: typeColor[i] }">{{ type }}</button>
            </div>
          </slide>
        </carousel>
      </div>
    </div>
  </div>
</template>

<script>
import { Carousel, Slide } from "vue-carousel";
import axios from "axios";
export default {
  name: "FilterCompo",
  components: {
    Carousel,
    Slide
  },
  data() {
    return {
      types: [],
      typesLoaded: false,
      typeColor: [
        "#ddd",
        "#c32",
        "#bbf",
        "#a4a",
        "#ec7",
        "#ca4",
        "#cd6",
        "#86a",
        "#79a",
        "#f83",
        "#79f",
        "#3c3",
        "#ca5",
        "#f38",
        "#aee",
        "#85f",
        "#554",
        "#fae",
        "#666",
        "#777"
      ]
    };
  },
  async created() {
    this.typesLoaded = false;
    await axios.get(`https://pokeapi.co/api/v2/type/`).then(res => {
      this.types = res.data.results.map(type => type.name);
      console.log(this.types);
      this.typesLoaded = true;
    });
  }
};
</script>

<style lang="scss">
.filter {
  margin-bottom: 30px;

  &__container {
    background: #fff;
    box-shadow: 0px 0px 31px -3px #d6d6d6;
    padding: 10px 5px;
    border-radius: 10px;
  }

  &__type-container {
    box-sizing: border-box;
    padding: 0 5px;
  }

  button {
    display: inline-block;
    margin-right: 9px;
    font-weight: 700;
    border: 0;
    width: 100%;
    max-width: 100%;
    text-align: center;
    padding: 10px 0;
    border-radius: 10px;
    font-size: 12px;
    text-transform: capitalize;
    letter-spacing: 0.04em;
    cursor: pointer;
    outline: 0;

    &:last-child {
      margin-right: 0;
    }
  }
}

@media screen and (max-width: 425px) {
  .filter {
    box-sizing: border-box;
    margin-bottom: 15px;
    padding: 0 15px;
  }
}
</style>