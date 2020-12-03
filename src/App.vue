<template>
  <div id="app">
    <FrontPage/>
  </div>
</template>

<script>
import FrontPage from './components/FrontPage.vue'

export default {
  name: 'App',
  components: {
    FrontPage
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}

pre{
  padding: 15px;
  border: 1px solid #e0e0e0;
  border-radius: 5px;
  background-color: #2c292d;
  color: #e0e0e0;
}

h1, h2, h3, h4, h5, h6{
  font-weight: 700;
}
</style>
