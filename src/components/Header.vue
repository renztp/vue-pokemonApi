<template>
  <header>
    <ul>
      <li>
        <router-link to="/">Home</router-link>
      </li>
      <li>
        <router-link to="/about">About</router-link>
      </li>
    </ul>
  </header>
</template>
<script>
export default {
  name: "Header"
};
</script>
<style lang="scss">
header {
  padding: 10px 0;
  margin: 20px 0 30px;

  ul {
    display: inline-block;
    padding: 0;
    margin: 0;
  }

  li {
    display: inline-block;
    margin-right: 30px;

    a {
      padding: 10px 0;
      text-decoration: none;
      color: #222;
      font-weight: 600;
      letter-spacing: 0.04em;
    }

    &:last-child {
      margin-right: 0;
    }
  }
}

@media screen and (max-width: 425px) {
  header {
    padding: 15px;
    margin: 0px 0 20px;
    border-bottom: 1px solid #ccc;
  }
}
</style>
