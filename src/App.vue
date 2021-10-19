<template>
  <div id="app">
    <Navbar />
    <Tasks />
  </div>
</template>

<script>
import Tasks from './components/Tasks.vue'
import Navbar from './components/Navbar.vue'

export default {
  name: 'App',
  components: {
    Tasks,
    Navbar,
  },
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
