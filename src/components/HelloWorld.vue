<template>
    <div class="wrapper">
    <!-- <h1>{{ msg }}</h1> -->
    <img class ='main-img' :src="mainImg" alt="unsplash img">
    <!-- <p>{{ story }}</p> -->
    <button class ='start-btn' @click="start = true" :class="{ start: start }">Click to Start</button>
  </div>
</template>

<script setup>
// export default {
//   name: 'HelloPotter',
//   props: {
//     // msg: String
//   }
// }
import { ref } from 'vue'
// import axios from 'axios';
const start = ref(false)
const mainImg = ref('');
mainImg.value = 'https://images.unsplash.com/photo-1518709268805-4e9042af9f23?crop=entropy&cs=srgb&fm=jpg&ixid=M3w1MzYzMDB8MHwxfHNlYXJjaHwxfHxtYWdpYyUyMHdvcmxkfGVufDB8fHx8MTcwMTgxNzg5OHww&ixlib=rb-4.0.3&q=85'

//magic world

const getRandomNumber = (min, max) => {
  return Math.floor(Math.random() * (max - min + 1)) + min;
}
  console.log(getRandomNumber);
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.wrapper {
  width: 800px;
  display: flex;
  gap: 12px;
  flex-flow: column nowrap;
  justify-content: center;
  align-items: center;
}
.start-btn {
  background-color: #000;
  border: none;
  color: white;
  padding: 15px 32px;
  text-align: center;
  font-size: 16px;
  border-radius: 12px;
  cursor: pointer;
}
.start {
  display: none;
}
.main-img {
  width: 600px;
  height: 450px;
  object-fit: cover;
}
</style>
