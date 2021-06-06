<template>
  <div id="app">
    <Api/>
  </div>
</template>

<script>
import Api from './components/Api.vue'

export default {
  name: 'App',
  components: {
    Api
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
