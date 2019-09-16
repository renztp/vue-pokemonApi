<template>
  <form class="poke-search">
    <input
      class="poke-search__search"
      type="text"
      v-model="currSearchVal"
      placeholder="Enter keyword..."
      v-on:input="updateSearch()"
    />
    <!-- Current Input value: {{ currSearchVal }} -->
  </form>
</template>

<script>
export default {
  name: "Search",
  props: {
    pokemonSearch: String
  },
  data() {
    return {
      currSearchVal: ""
    };
  },
  methods: {
    updateSearch: function() {
      this.$emit("updateSearch", this.currSearchVal);
    }
  }
};
</script>

<style lang="scss">
.poke-search {
  position: relative;
  width: 100%;

  &__search {
    display: inline-block;
    width: 100%;
    padding: 8px;
    border-radius: 10px;
    border: 0;
    box-shadow: 0px 0px 31px -3px #d6d6d6;
    margin-bottom: 30px;
    box-sizing: border-box;
    background: white;
    outline: none;
  }
}

@media screen and (max-width: 425px) {
  .poke-search {
    box-sizing: border-box;
    padding: 0 15px;
  }
}
</style>