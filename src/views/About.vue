<template>
  <div class="about">
    <section class="intro">
      <h1>Introduction</h1>
      <p>
        A simple Vuejs App using axios to call data from the
        <a
          href="https://pokeapi.co/"
          target="_blank"
        >pokemonApi</a>.
      </p>
      <h2>Purpose of making the app</h2>
      <p>I have taken an interest on Vuejs recently, after learning and exposing myself in the world of react world and decided to learn vuejs because i heard it has more of a friendly learning curve. I have only done todolist apps using react so instead of doing the same app on vuejs, i have decided to make an application that calls some data to a rest API.</p>
      <h2>Technologies used</h2>
      <p>
        <a href="https://vuejs.org/" target="_blank">Vuejs</a>,
        <a href="https://pokeapi.co/" target="_blank">PokemonApi</a>,
        <a href="https://vue-chartjs.org/" target="_blank">vue-chartjs</a>
      </p>
      <h2>Tools used</h2>
      <p>Vscode, WSL(Windows System for linux), Vue-cli</p>
    </section>
    <section class="links">
      <h2>Links</h2>
      <ul>
        <li>
          <b>Github:</b>
          <a href="https://github.com/renzpulvira" target="_blank">renzpulvira</a>
        </li>
        <li>
          <b>Twitter:</b>
          <a href="https://twitter.com/renztp1" target="_blank">@renztp</a>
        </li>
      </ul>
    </section>
  </div>
</template>

<script>
export default {
  name: "About"
};
</script>

<style lang="scss">
.about {
  h1,
  h2 {
    font-size: 20px;
    padding: 0 0 5px;
    margin: 0;
  }

  a {
    color: #666;
  }
  .intro {
    p {
      font-size: 16px;
      margin: 0 0 30px;
      padding: 0;
    }
  }

  .links {
    ul {
      list-style: none;
      padding: 0;
      margin: 0;
    }
  }
}

@media screen and (max-width: 425px) {
  .about {
    box-sizing: border-box;
    padding: 0 15px;
    margin: 0 0 30px;
  }
}
</style>
