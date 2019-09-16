<template>
  <div class="pokemon-modal">
    <img class="pokemon-modal__close" v-on:click="toggleClose()" src="@/assets/close.png" />
  </div>
</template>

<script>
export default {
  name: "PokemonSingle",
  props: {
    isOpen: Boolean
  },
  methods: {
    toggleClose: function() {
      this.$emit("toggleClose", false);
    }
  }
};
</script>


<style lang="scss" scoped>
.pokemon-modal {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  height: 80vh;
  width: 60%;
  background: white;
  border: 2px solid #333;
  z-index: 9;

  &__close {
    position: absolute;
    top: 10px;
    right: 10px;
    width: 16px;
    cursor: pointer;
  }
}
</style>