<template>
  <!-- <img alt="Vue logo" src="./assets/logo.png"> -->
  <HelloPotter />
  <!-- msg="Welcome to Your Vue.js App" -->
</template>

<script>
import HelloPotter from './components/HelloWorld.vue'

export default {
  name: 'App',
  components: {
    HelloPotter
  }
}
</script>

<style>
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
