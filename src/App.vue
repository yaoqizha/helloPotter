<template>
  <HelloPotter />
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
    width: 100%;
    height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;
  }
</style>
