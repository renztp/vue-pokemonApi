<template>
  <div id="app">
    <Header />
    <router-view />
  </div>
</template>

<script>
import Header from "@/components/Header.vue";
export default {
  components: {
    Header
  }
};
</script>

<style lang="scss">
@import "./assets/normalize.css";
@font-face {
  font-family: "Roboto";
  src: url("./assets/fonts/roboto-regular.ttf") format("ttf"),
    url("./assets/fonts/roboto-regular.woff") format("woff"),
    url("./assets/fonts/roboto-regular.woff2") format("woff2");
  font-style: norma;
}

body {
  padding: 0;
  margin: 0;
  background: #8c8c8c14;
}

#app {
  width: 100%;
  max-width: 770px;
  padding: 0;
  margin: 0 auto;
  box-sizing: border-box;
  font-family: "Roboto", "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}

#nav {
  padding: 30px;
  a {
    font-weight: bold;
    color: #2c3e50;
    &.router-link-exact-active {
      color: #42b983;
    }
  }
}
</style>
