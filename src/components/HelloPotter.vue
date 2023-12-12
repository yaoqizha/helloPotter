<template>
  <div class="wrapper">
    <!-- <h1>{{ msg }}</h1> -->
    <img class ='main-img' :src="mainImg" alt="unsplash img">
    <div class="bottom-text" :class="{ start: start }">
      <div class="text-area">
        <p>{{ story }}"Harry Potter，被選者之一，以他的蛇語能力、守護神（鹿）和逐影术而著稱。作為成為哈利好朋友的領袖，他充滿勇氣和毅力，是黑魔王的堅定敵人。"</p>
        <ul><li v-for="option in options" :key = option.id>
          <label for="option.content"><input v-model="selectedOption" :id="option.index" :value="option.content" type="radio">{{ option.content }}</label>
          </li>
        </ul>
      </div>
      <button class ='start-btn' @click="startGame()">Click to Start</button>
    </div> 
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import axios from 'axios';
import charactersData from '../assets/characters.json'

const characters = charactersData;
const start = ref(false)
const mainImg = ref('');
let options = ref([]);
let selectedOption = ref([]);
let story = ref('');
let round = 0;


const prefix =[
{
  role:'system',
  content:'現在對話的背景是哈利波特的魔法世界，我在進行巫師對決，我的角色設定皆為霍格華滋陣營，你是輔助系統幫助我完成故事與對決'
},{
  role:'user',
  content: '以下是我的角色資料：{ "name": "哈利波特", "skill": "魔杖技巧", "attack": 8, "health": 9 }'
}
]
const script = [
  {
      role: 'user',
      content: '我現在是進行巫師對決的玩家，請給我三個霍格華滋陣營的角色讓我選擇，請用JSON格式回答，範例為 { "name": // 角色名稱，小說中出現過的, "skill": // 角色技能, "attack": // 攻擊力，數值為5-10，請根據角色特性給予, "health": // 生命值，請根據角色特性給予 }，我要直接做JSON.parse，請不要給我不能用的字元'
  },
  // {
  //     role: 'user',
  //     content: '我遇到了一個食死徒，遭遇一場戰鬥，簡單敘述戰鬥內容跟我角色的資料變化，角色的JSON資料請放在敘述結束後的 DATA: 後方'
  // }
];
async function getStory() {
  const baseUrl = 'https://api.openai.com/v1/chat/completions';
  const MODEL = 'gpt-3.5-turbo';
  const token = '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';
  const response = await axios.post(
      baseUrl,
      {
        messages: [
        ...prefix,
        script[round],
        ],
        //prompt: prompt,
        model: MODEL,
        max_tokens: 150,
        temperature: 0.9,
        top_p: 1,
        n: 1,
        //stream: false,
        // logprobs: null,
        // stop: ['\n', "DATA:"],
      },
      {
        headers: {
          'Content-Type': 'application/json',
          'Authorization': `Bearer ${token}`
        }
      }
    ); 
  story.value = response?.data?.choices[0]?.message.content
}
const teams = computed(() => {
  return [...new Set(characters.map(character => character.team))];
})
selectedOption.value =  options.value.filter(option => option.isSelected === true)
const teamHarry = computed(() => {
  return characters.filter(character => character.team === '鳳凰會');
})
const teamVoldemort = computed(() => {
  return characters.filter(character => character.team === '食死徒');
})
function startGame() {
  start.value = true
  story.value = '請選擇成為食死徒或是鳳凰會'
  setOptions(teams.value)
}
if(selectedOption.value.length > 0) {
  if(selectedOption.value[0] === '鳳凰會') {
    story.value = '請選擇你的角色'
    setOptions(teamHarry.value.map(character => character.name))
  } else if(selectedOption.value[0] === '食死徒') {
    story.value = '請選擇你的角色'
    setOptions(teamVoldemort.value.map(character => character.name))
  }
}


function setOptions (arr) {
  let temp = []
  arr.forEach((item, index) => {
    temp.push({
      id: index,
      content: item,
      isSelected: false
    })
  })
  options.value = temp
}
window.getStory = getStory
//getStory()
// const getPictures = async ({ keyword }) => {
//   const baseUrl = 'https://api.unsplash.com/search/photos';
//   const response = await axios.get(
//     `${baseUrl}?query=${keyword}&client_id=lpzI5L4f2klqot43me98raxpI6FVeWHCbkSQuo9LM9Q`
//   );

//   background.value = response.data.results[getRandomNumber(10)].urls.regular;
//   console.log(background.value)
  
// }

const getRandomNumber = (min, max) => {
  return Math.floor(Math.random() * (max - min + 1)) + min;
}
window.getRandomNumber = getRandomNumber;
//getPictures({ keyword: 'Light vs Dark confrontation' });
mainImg.value = 'https://images.unsplash.com/photo-1518709268805-4e9042af9f23?crop=entropy&cs=srgb&fm=jpg&ixid=M3w1MzYzMDB8MHwxfHNlYXJjaHwxfHxtYWdpYyUyMHdvcmxkfGVufDB8fHx8MTcwMTgxNzg5OHww&ixlib=rb-4.0.3&q=85'

// const getStory = async () => {
//   const baseUrl = 'https://www.potterapi.com/v1/sortingHat';
//   const response = await axios.get(
//     `${baseUrl}`
//   );
//   console.log(response.data);
// }
//hogwart, hogwarts express, harry potter, harry potter studio london, harry, dark forest, dark cloud
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
ul {
  list-style-type: none;
  padding: 0;
  margin: 0;
  width: 100%;
}
li {
  display: inline-block;
  width: 100%;
}
li:not(:last-child) {
  margin-bottom: 8px;
}

.wrapper {
  width: 800px;
  height: 600px;
  display: flex;
  gap: 12px;
  flex-flow: column nowrap;
  justify-content: center;
  align-items: center;
}
.bottom-text {
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
}
.text-area {
  display: none;
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
  display: block
}
.start .start-btn {
  display: none;
}
.start .text-area {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  gap: 12px;
}
.main-img {
  display: inline-block;
  width: 600px;
  height: 450px;
  object-fit: cover;
}
</style>
