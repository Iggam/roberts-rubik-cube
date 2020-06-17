<template>
  <div id="app">
    <Rubik3x3Model/>
  </div>
</template>

<script>
import Rubik3x3Model from '@/components/Rubik3x3Model.vue'

export default {
  name: 'App',
  components: {
    Rubik3x3Model
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  background-color: #c4c3d4;
  color: #2c3e50;
  height: 100vh;
}
</style>
