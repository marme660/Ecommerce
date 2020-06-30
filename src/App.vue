<template>
  <div id="app">
    <DisplayProduct/>
  </div>
</template>

<script>
import DisplayProduct from './components/DisplayProduct.vue'

export default {
  name: 'App',
  components: {
    DisplayProduct
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
