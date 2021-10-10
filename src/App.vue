<template>
  <div id="app">
    <!-- <HelloWorld msg="Welcome to Your Vue.js App"/> -->
    <ThreeView />
  </div>
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'
import ThreeView from './components/ThreeView.vue'
export default {
  name: 'App',
  components: {
    // HelloWorld,
    ThreeView
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
}
</style>
