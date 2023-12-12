<template>
  <HelloPotter msg="Welcome to Hogwarts"/>
</template>

<script>
import HelloPotter from './components/HelloPotter.vue'

export default {
  name: 'App',
  components: {
    HelloPotter
  }
}
</script>

<style>
body {
  margin: 0;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  width: 100%;
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
